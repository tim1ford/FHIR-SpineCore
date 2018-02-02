---
title: Endpoint Lookup Example - SSP
keywords: build, directory, endpoints
tags: [design]
sidebar: overview_sidebar
permalink: build_endpoints_example_ssp.html
summary: "Worked example of looking up a Spine endpoint from SDS for calls brokered through SSP"
---

*NOTE*: Based on: https://developer.nhs.uk/apis/gpconnect/integration_spine_directory_service.html

{% include important.html content="All information provided below is indicative and subject to on-going review." %}

## Worked example of the endpoint lookup process ##

**Given**
A consuming system which needs to get the HTML View of a patient record at the patient's registered practice. The consuming system has the following information about the patient:
- NHS Number
- A set of demographic details about the patient

**When**
The consuming system interacts with GP Connect

**Then** 
The following steps MUST be followed:


### Step 0. PDS Trace (pre-requisite step)

The Consuming system is responsible for [performing a PDS Trace](pds_overview.html) to both verify the identity of the patient and retrieve the ODS code of the patient's registered primary care practice. 

For this example, NHS Number 9000000084 with demographic details Mr Anthony Tester, 19 Ficticious Avenue, Testtown returns the ODS code T99999


### Step 1. Accredited System Lookup on SDS

The ASID and Party Key is now looked up on SDS. The example below uses ldapsearch:
	
	ldapsearch -x -H ldaps://ldap.vn03.national.ncrs.nhs.uk –b "ou=services, o=nhs" 
	"(&(nhsIDCode=T99999) (objectClass=nhsAS)(nhsAsSvcIA=urn:nhs:names:services:gpconnect:fhir:operation:gpc.getcarerecord))" 
	uniqueIdentifier nhsMhsPartyKey
	
This query should return a single matching accredited system object from SDS, the ASID being found in the uniqueIdentifier attribute. In the case, ldapsearch returns the following results:


	999999999999, Services, nhs
	dn: uniqueIdentifier=9999999999,ou=Services,o=nhs
	uniqueIdentifier: 999999999999
	nhsMhsPartyKey: T99999-9999999

	# search result
	search: 1
	result: 0 Success

	
### Step 2: MHS lookup on SDS to determine FHIR endpoint Server Root URL

Using the Party Key retrieved from Step 1, and the same interaction ID, the following ldapsearch query is executed:

	ldapsearch -x -H ldaps://ldap.vn03.national.ncrs.nhs.uk -b "ou=services, o=nhs" 
	"(&(nhsMhsPartyKey=T99999-9999999) (objectClass=nhsMhs) (nhsMhsSvcIA=urn:nhs:names:services:gpconnect:fhir:operation:gpc.getcarerecord))" 
	nhsMhsEndPoint nhsMHSFQDN
	

This query should again return a single endpoint. In this case, the ldapquery returns the following results:

	# 472b35d4641b76454b13, Services, nhs
	dn: uniqueIdentifier=472b35d4641b76454b13,ou=Services,o=nhs
	nhsMhsEndPoint: https://pcs.thirdparty.nhs.uk/T99999/DSTU2/1
	nhsMHSFQDN: pcs.thirdparty.nhs.uk

	# search result
	search: 2
	result: 0 Success
	


### Step 3: Consumer constructs full GP Connect request URL to be sent to the Spine Security Proxy

The format of the full URL which the consuming sytem is responsible for constructing is as follows:

`https://[URL of Spine Security Proxy]/[Provider Server Root URL]/[FHIR request]`

The value returned in the nhsMhsEndPoint attribute in Step 2 should be treated as the FHIR Server Root URL at the provider system.

In this example, to issue a GetCareRecord request, the following request would be made:

`POST https://testspineproxy.nhs.domain.uk/https://pcs.thirdparty.nhs.uk/T99999/DSTU2/1/Patient/$gpc.getcarerecord`



### Provider System Viewpoint ###

For systems providing APIs to be accessed through the [Spine Secure Proxy](ssp_overview.html), the provider system is responsible for populating SDS with the necessary information to enable reliable endpoint lookup by consuming systems.

In order to ensure that endpoint lookup is reliable, the following guidelines must be followed for First of Type implementations:

**1. Format of Server Root URL**

The *Server Root URL* for a given ASID SHALL be defined in the nhsMhsEndPoint attribute of the MHS record (i.e. the ldap object of type nhsMhs). This URL SHALL be in the format described in the [API Versioning](development_general_api_guidance.html#fhir-api-versioning) guidance.


**2. For First of Type interactions, CMA type endpoints only will be used**

A CMA type endpoint refers to an endpoint which is a combined MHS sytem and Accredited System endpoint. There will be a 1-1 mapping between an Accredited System (uniquely identified by an ASID) and a Message Handling System (MSH). A single MHS record SHALL be associated with a given ASID and interaction ID.

**3. nhsMhsEndPoint attribute SHALL contain the FHIR Server Root URL only**

The nhsMHsEndPoint attribute SHALL contain the *FHIR Server Root URL*. It is the responsibility of the consuming system to construct the FHIR operation or RESTful resource request which will be postfixed to this base URL.

I.e. an example of a FHIR server root URL for a GetCareRecord interaction at practice GP0001 would be

`https://provider.thirdparty.nhs.uk/GP0001/DSTU2/1`

Note that the "Patient/$gpc.getcarerecord" is not added.

In line with this, provider systems SHOULD perform checks that the FHIR request received is a reasonable means to request the resource in view given the specificed interaction. 


**4. Practice routing identifier to be included in FHIR Server Root URL**

As described in the [API Versioning](development_general_api_guidance.html#fhir-api-versioning) guidance, a routing identifier SHALL be placed in the FHIR Servder Root URL. This routing identifier may be the ODS code of the practice, or another logical identifier which acheives reliable routing of the request to the patient's registered practice data store. It is expected that the FHIR server business logic will extract the routing identifier.

In line with this, HTTP headers SHALL NOT be used to provide this organisation routing.

**5. Practice specific ODS codes to be used**

ODS codes which refer to Principle Clinical Systems as a single entity SHALL NOT be used to provide routing. Practice specific ODS codes SHALL be used for routing purposes in the FHIR Server Root URL found in the NhsMhsEndPoint attribute of the MHS record.


**6. The FHIR Server Root URL SHALL contain the FHIR version name**

The FHIR Server Root URL defined in the nhsMhsEndPoint attribute SHALL contain the FHIR version name as described in the [API Versioning](development_general_api_guidance.html#fhir-api-versioning) guidance. This will enable versioning of provider API by FHIR version. 

In line with this, provider systems SHALL NOT version through the use of HTTP headers.


**7. FHIR version SHALL match version found in FHIR conformance statement**

The FHIR version as returned in a [CapabilityStatement](foundations_use_case_get_the_fhir_capability_statement.html) from the FHIR server which services the FHIR request SHALL match the FHIR version given in the FHIR Server Root URL.

**8. FHIR Server Root URLs associated with a given product set SHALL use same FHIR Version**

Where a provider moves in future to later version of FHIR, it will be necessary to define a new product set to accommodate the set of interactions provided by this. FHIR Server Root URLs defined for a specific product set SHALL all reference the same FHIR version. This ensures that FHIR resources references returned in FHIR responses are locally resolvable. 

For example, all interactions associated with the Appointment Management capability in a given product set must refer to the same FHIR server, so that the resource references for "Read Appointment" and "Amend" appointment would be locally resolvable to the same resource on the same FHIR Server. 


**9. Acceptable use of ASID information in HTTP Headers**

Source and destination ASID information is passed to the provider system from the Spine Security Proxy. Providers SHALL use this information for audit and debugging purposes only, and SHALL NOT use these headers to perform routing or lookups. 

It is the responsibility of the Spine Security Proxy SSP to perform lookups to determine consumer accreditation status. Routing shall be carried out as described above through practice specific ODS codes present in the FHIR Server Root URL. 




