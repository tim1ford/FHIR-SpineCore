---
title: Spine Endpoints
keywords: build, directory, endpoints
tags: [design]
sidebar: overview_sidebar
permalink: build_endpoints.html
summary: "SDS allows details of registered system endpoints to be looked up"
---

{% include important.html content="All information provided below is indicative and subject to on-going review." %}

## Spine Accredited Systems and Endpoints ##

Every system that connects to the Spine has one or more "Accredited System" entry in the Spine Directory, identified by an Accredited System Identifier (ASID).

This ASID is unique to a system deployed in a specific organisation, so the same application deployed into three NHS organisations would typically be represented as three unique ASIDs.

In addition, each endpoint that is registered with the Spine has one or more "interaction IDs" associated with the endpoint. These identify the types of Spine interaction that the endpoint has been assured to perform. These are used to control what functionality can be called by connecting systems - calls to interactions for which the accredited system has not been assured will be blocked.

### Terminology ###

| **MHS** | Message Handling Server. A middleware that handles messaging to/from Spine. |
| **ASID** | Accredited System Identifier. A unique number allocated to a system on accreditation for connection to Spine. |
| **MHS Endpoint** | An endpoint registered with Spine for use with multiple systems via a MHS. Each system has its own ASID. |
| **CMA Endpoint** | Combined MHS and Accredited System Endpoint. An endpoint registered with Spine for a single system. |

### Consuming Spine Services ###

A consuming system will interact with SDS in order to resolve the FHIR Endpoint Server Root URL to be used when constructing the request to be made to the Spine service it wishes to call (or another external endpoint when sending a brokered call through the [Spine Secure Proxy]()).

This is a two step process, as follows:

1. Lookup the Message Handling System (MHS)
2. Lookup the Accredited System ID (ASID)


Once the MHS record has been retrieved the fully qualified domain name (FQDN) and full endpoint of the FHIR server can be retrieved from returned attributes of the MHS record.

Systems **SHOULD** cache SDS query results giving details of consuming system, endpoints and endpoint capability on a per session basis.

Consuming systems **SHALL NOT** cache and re-use consuming system, endpoint information derived from SDS across multiple patient encounters or practitioner usage sessions. Each new patient encounter will result in new lookups to ascertain the most up-to-date consuming system, endpoint and endpoint capability.


## Step 1a: Message Handling System (MHS) Lookup for an external service (brokered via SSP)  ##

Consumers **SHALL** lookup the endpoint URL, FQDN and Party Key from the MHS record, as follows:

**Search criteria:**
- Organisational code
	- `nhsIDCode` = *[odsCode]* of the target organisation (e.g. GP practice)
- Message Handling System type
	- `objectClass` = `nhsMHS`
- MHS Interaction ID
	- `nhsMhsSvcIA` = *[interactionId]* of the API operation required

**Result attributes:**
- Endpoint URL
	- `nhsMhsEndPoint` 
- Fully qualified domain name
	- `nhsMHSFQDN` * 
- Party Key
	- `nhsMhsPartyKey`

**ldapsearch query:**

```bash
ldapsearch -x -H ldaps://ldap.vn03.national.ncrs.nhs.uk -b "ou=services, o=nhs" 
	"(&(nhsidcode=[odsCode]) (objectClass=nhsMhs) (nhsMhsSvcIA=[interactionId]))"
	nhsMhsEndPoint, nhsMHSFQDN, nhsMhsPartyKey	
```

{% include note.html content="The FHIR endpoint URL of the message handling system can then be extracted from the `nhsMhsEndPoint` attribute of the MHS record. <br/><br/><b>*</b> The attribute `nhsMhsFQDN` can be used to retrieve the FQDN of the endpoint, though this can be extracted from the nhsMhsEndPoint." %}

Please refer to the specification of the specific FHIR API you are using for details of the interaction ID to use:

- [GP Connect operation guidance](https://developer.nhs.uk/apis/gpconnect/development_fhir_operation_guidance.html) for details of the GPConnect interactionId appropriate for your use case.



## Step 1b: Message Handling System (MHS) Lookup for a National Spine Service ##

When the client wants to make a call to a national Spine service (e.g. NRLS, Visitors and Migrants, etc), the first step is to establish the Party Key for the Spine (there is a single Party Key for each Spine environment. This is fixed for each environment, so does not need to be looked up on SDS (step 1a) - to find the Party Key for the Spine environment refer to the "Authority Service Names" document for the relevant Spine environment - see [Environments](test_environments.html) for details.


## Step 2a: Accredited System ID (ASID) Lookup for an external service (brokered via SSP) ##

When the client wants to query an external service brokered through the Spine Security Proxy (e.g. a GP Connect API), the client **SHALL** use an organisation ODS code for the target organisation and the nhsMHSPartyKey (identified in step 1) to lookup the Accredited System ID (ASID) as follows:

**Search criteria:**
- Organisational code
	- `nhsIDCode` = *[odsCode]* of the target organisation (e.g. GP practice)
- Accredited System type
	- `objectClass` = `nhsAs`
- MHS Party Key
	- `nhsMHSPartyKey` = *[partyKey]* as retrieved from the `nhsMhsPartyKey` attribute in step 1a
	
**Result attributes:**
- ASID
	- `uniqueIdentifier`

**ldapsearch query:**
	
```bash
ldapsearch -x -H ldaps://ldap.vn03.national.ncrs.nhs.uk –b "ou=services, o=nhs" 
	"(&(nhsidcode=[odsCode]) (objectclass=nhsAs) (nhsMHSPartyKey=[nhsMHSPartyKey]))"
	uniqueIdentifier	
```

The ASID will be returned in the uniqueIdentifier attribute which is returned from the ldapsearch query above.

{% include note.html content="Note that ldapsearch is used to establish a TLS session rather than the StartTLS option. Also note that once the TLS session is established, SASL authentication is not used by SDS and is therefore disabled through the -x option." %}


## Step 2b: Accredited System ID (ASID) Lookup for a National Spine Service ##

?????????? Ask Adam.




## ldapsearch configuration ##

SDS requires TLS Mutual Authentication. It is therefore necessary to configure ldapsearch in the examples above with the certificates necessary to verify the authenticity of the SDS LDAP server, and also to enable SDS to verify the spine endpoint making the LDAP request:

1. Root and SubCA spine development certificates available from Assurance Support
2. Obtain a client certificate by submitting a certificate signing request for your development endpoint to Assurance Support

## Server certificate setup ##
For the examples above, ldapsearch should be configured to find the RootCA and SubCA certificates using the TLS_CACERT option in the ldap.conf file. This should point to a file, in PEM format, which contains both root and subca certificates ensuring that the root certificate is placed after the subCA certificae. The LDAPCONF environment vairable can be used to define the location of the ldap.conf 

## Client certificate setup ##
The client certificate and encrypted private key should be defined in the .ldaprc file using the following directives.

`
TLS_CERT C:\mydir\cert.pem
TLS_KEY C:\mydir\key.pem
`

The location of the .ldaprc file can be defined using the LDAPRC environment variable.

See [Environments](test_environments.html) for details of where to find URLs for SDS.

