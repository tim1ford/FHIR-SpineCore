---
title: Access Tokens and Audit (JWT)
keywords: spine, ssp, integration, authorisation, audit, provenance
tags: [design]
sidebar: overview_sidebar
permalink: security_jwt.html
summary: "Overview of how authorisation information is used and passed in APIs for audit and provenance."
---

*NEEDS SIGNIFICANT RE-WRITING AND SIMPLIFYING BASED ON WORKSHOP OUTPUTS*

## Use of Bearer Tokens ###

An output of [authorising access](security_authorisation.html) to an API is the provision of a JSON Web Token. This MUST be passed in the API calls to ensure the systems being called are able to verify that the user has been authorised to see the resources requested. This JWT is also used for audit purposes, so the API implementation (and the SSP in the case of a call brokered through that service) can record the user context in it's audit trail.

In order to achieve this, the Consumer MUST include Access token in the HTTP authorisation header as an oAuth Bearer Token (as outlined in [RFC 6749](https://tools.ietf.org/html/rfc6749){:target="_blank"}) in the form of a JSON Web Token (JWT) as defined in [RFC 7519](https://tools.ietf.org/html/rfc7519){:target="_blank"}.

An example such an HTTP header is given below:

```
     Authorization: Bearer jwt_token_string
```

API Provider systems SHALL respond to oAuth Bearer Token errors in line with [RFC 6750 - section 3.1](https://tools.ietf.org/html/rfc6750#section-3.1).

It is highly recommended that standard libraries are used for creating the JWT as constructing and encoding the token manually may lead to issues with parsing the token. A good source of information about JWT and libraries to use can be found on the [JWT.io site](https://jwt.io/)


## JWT without an Authorisation Server ##

The new national authentication service is not yet in place, so it is not currently possible to request an Access token from that service. In the interim, Consumer systems are expected to either use a local service to generate tokens, or generate a new JWT for each API request themselves. A consumer generated JSON Web Token (JWT) SHALL consisting of three parts seperated by dots `.`, which are:

- Header
- Payload
- Signature

### Header ###

Where Consumer systems are generating the JWT locally, they SHALL generate an Unsecured JSON Web Token (JWT) using the 'none' algorithm parameter in the header to indicate that no digital signature or MAC has been performed (please refer to section 6 of [RFC 7519](https://tools.ietf.org/html/rfc7519){:target="_blank"} for details).

```json
{
  "alg": "none",
  "typ": "JWT"
}
```

### Payload ###

Consumers systems SHALL generate a JWT Payload conforming to the requirements set out in the [JWT Payload](#jwt-payload) section of this page.

### Signature ###

Consumer systems SHALL generate an empty signature.

### Complete JWT ###

The final output is three Base64url encoded strings separated by dots (note - there is some canonicalisation done to the JSON before it is Base64url encoded, which the JWT code libraries will do for you).

```shell
eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0.eyJpc3MiOiJodHRwOi8vZWMyLTU0LTE5NC0xMDktMTg0LmV1LXdlc3QtMS5jb21wdXRlLmFtYXpvbmF3cy5jb20vIy9zZWFyY2giLCJzdWIiOiIxIiwiYXVkIjoiaHR0cHM6Ly9hdXRob3JpemUuZmhpci5uaHMubmV0L3Rva2VuIiwiZXhwIjoxNDgxMjUyMjc1LCJpYXQiOjE0ODA5NTIyNzUsInJlYXNvbl9mb3JfcmVxdWVzdCI6ImRpcmVjdGNhcmUiLCJyZXF1ZXN0ZWRfcmVjb3JkIjp7InJlc291cmNlVHlwZSI6IlBhdGllbnQiLCJpZGVudGlmaWVyIjpbeyJzeXN0ZW0iOiJodHRwOi8vZmhpci5uaHMubmV0L0lkL25ocy1udW1iZXIiLCJ2YWx1ZSI6IjkwMDAwMDAwMzMifV19LCJyZXF1ZXN0ZWRfc2NvcGUiOiJwYXRpZW50LyoucmVhZCIsInJlcXVlc3RpbmdfZGV2aWNlIjp7InJlc291cmNlVHlwZSI6IkRldmljZSIsImlkIjoiMSIsImlkZW50aWZpZXIiOlt7InN5c3RlbSI6IldlYiBJbnRlcmZhY2UiLCJ2YWx1ZSI6IkdQIENvbm5lY3QgRGVtb25zdHJhdG9yIn1dLCJtb2RlbCI6IkRlbW9uc3RyYXRvciIsInZlcnNpb24iOiIxLjAifSwicmVxdWVzdGluZ19vcmdhbml6YXRpb24iOnsicmVzb3VyY2VUeXBlIjoiT3JnYW5pemF0aW9uIiwiaWQiOiIxIiwiaWRlbnRpZmllciI6W3sic3lzdGVtIjoiaHR0cDovL2ZoaXIubmhzLm5ldC9JZC9vZHMtb3JnYW5pemF0aW9uLWNvZGUiLCJ2YWx1ZSI6IltPRFNDb2RlXSJ9XSwibmFtZSI6IkdQIENvbm5lY3QgRGVtb25zdHJhdG9yIn0sInJlcXVlc3RpbmdfcHJhY3RpdGlvbmVyIjp7InJlc291cmNlVHlwZSI6IlByYWN0aXRpb25lciIsImlkIjoiMSIsImlkZW50aWZpZXIiOlt7InN5c3RlbSI6Imh0dHA6Ly9maGlyLm5ocy5uZXQvc2RzLXVzZXItaWQiLCJ2YWx1ZSI6IkcxMzU3OTEzNSJ9LHsic3lzdGVtIjoibG9jYWxTeXN0ZW0iLCJ2YWx1ZSI6IjEifV0sIm5hbWUiOnsiZmFtaWx5IjpbIkRlbW9uc3RyYXRvciJdLCJnaXZlbiI6WyJHUENvbm5lY3QiXSwicHJlZml4IjpbIk1yIl19fX0.
```

NOTE: The final section (the signature) is empty, so the JWT will end with a trailing `.` (this must not be omitted, otherwise it would be an invalid token).


## JWT Payload ##

The Payload section of the JWT shall be populated as follows:

| Claim | Priority | Description | Fixed Value | Dynamic Value |
|-------|----------|-------------|-------------|------------------|
| iss | R | Requesting systems issuer URI | No | Yes |
| sub | R | ID for the user on whose behalf this request is being made. Will match either the `requesting_user` or `requesting_patient` | No | Yes |
| aud | R | API endpoint URL | No | Yes |
| exp | R | Expiration time integer after which this authorization MUST be considered invalid. | No | (now + 5 minutes) UTC time in seconds |
| iat | R | The UTC time the JWT was created by the requesting system | No | now UTC time in seconds |
| reason_for_request | R | Purpose for which access is being requested | `directcare`, `secondaryuses` or `patientaccess` | No |
| requested_scope | R | Data being requested | `patient/*.[read|write]` <br/>OR <br/>`organization/*.[read|write]` | No |
| requesting_system | R | Identifier for the system or device making the request | No | System or Device Identifier |
| requesting_organization | R | Organisation making the request | No | Organisation Identifier | 
| requesting_user | R | Health or Social Care professional making the request | No | User Identifier |
| requesting_patient | R | Citizen making the request | No | NHS Number |


### JWT Payload Example ###

```json
{
	"iss": "https://cas.nhs.uk",
	"sub": "https://fhir.nhs.uk/Id/sds-role-profile-id"|[SDSRoleProfileID]",
	"aud": "https://provider.thirdparty.nhs.uk/GP0001/STU3/1",
	"exp": 1469436987,
	"iat": 1469436687,
	"reason_for_request": "directcare",
	"requested_scope": "patient/*.read",
	"requesting_system": "https://fhir.nhs.uk/Id/accredited-system|[ASID]",
	"requesting_organization": "https://fhir.nhs.uk/Id/ods-organization-code|[ODSCode]",
	"requesting_user": "https://fhir.nhs.uk/Id/sds-role-profile-id"|[SDSRoleProfileID]"
}
```

Where the Practitioner has both a local system identifier but no Spine identifier, then the a local identifier MAY be used with a suitable URI to identify the system/organisation that assigned the ID.

### Identifier Prefixes ###

All identifiers used in the JWT follow the same approach used for other FHIR identifiers - i.e. they have a prefix to identify the naming system the identifier comes from. This allows different naming systems to be used without any risk of confusion if the same identifier exists in different naming systems. The identifier should be a string, with the naming systen URI followed by a pipe symbol (\|), followed by the identifier value - i.e.:

```
[Naming system URI]|[Identifier]
```

For example:

```
https://fhir.nhs.uk/Id/ods-organization-code|X09
```

Naming systems appropriate for specific types of identifiers are listed in the population guidance below.

### Population of JWT attributes ###

Common attributes are as defined in [rfc7519](https://tools.ietf.org/html/rfc7519#section-4.1.3){:target="_blank"} - but the below table provides specific guidance on the values of specific attributes:

| Attribute Name | Attribute Value Guidance |
| iss | (Issuer) For tokens issued by the national authentication solution, this will be the URL of that national service. For tokens issued by a different issuer, this will hold the URL for the issuer. |
| sub | (Subject) An identifier for the person or system that has been authorised for access. In most cases this will be an identifier for a member of staff, identified by a Spine URP ID (see [RBAC](security_rbac.html)) for details. In cases where the API is being accessed by a citizen, this will be the NHS Number of that individual. |
| aud | (Audience) The URI for the API Endpoint. This will be the fully qualified endpoint address returned to the Consumer by the [SDS endpoint lookup service](build_endpoints.html) as the value of `nhsMhsEndPoint`. |
| reason_for_request | This identified the purpose for which the request is being made. This is currently limited to one of the following values: **directcare**, **secondaryuses** or **patientaccess**. |
| requested_scope | This is a space-separated list of the [scopes](security_scopes.html) authorised for this user. Individual APIs will check this list to establish whether the authorisation grants access to the data being request by the client, and will reject the call if the appropriate scopes have not been granted in the token. |
| requesting_system | This is an identifier for the deployed client system that has been authorised to make API calls. In the case of Spine-enabled clients (or those using the [SSP](ssp_overview.html) to broker API calls), this will be a [Spine Accredited System ID](build_endpoints.html) (ASID)<br/>The naming system prefix for the ASID will be **https://fhir.nhs.uk/Id/accredited-system** |
| requesting_organization | This is the ODS code of the care organisation from where the request originates.<br/>The naming system prefix for the ODS code will be **https://fhir.nhs.uk/Id/ods-organization-code** |
| requesting_practitioner | If this authorisation relates to a member of staff, this attribute will hold the Spine URP ID (see [RBAC](security_rbac.html)) for details. In this case, the value of this attribute will match the "sub" attribute value.<br/>The naming system prefix for the URP ID code will be **https://fhir.nhs.uk/Id/sds-role-profile-id**<br/>Where national authorisation is not being used (e.g. where Spine is brokering a call to a local system that has authorised the API access directly), and a national SDS role profile ID can't be used, a local user identifier may be used instead.<br/>The naming system prefix for a local identifier will be a locally-defined URI specific to the system/service that managed the user identities - e.g. **https://my-care-service/Id/user-id**<br/>Where the user has both a local system 'role' as well as a nationally-recognised role, then the latter SHALL be provided. Default usernames (e.g. referring to systems or groups) SHALL NOT be used in this field. | |
| requesting_patient | If this authorisation relates to a citizen, this attribute will hold the NHS Number of the citizen<br/>The naming system prefix for the NHS Number will be **http://fhir.nhs.net/Id/nhs-number** |


*TODO - Review whether we need/can use the endpoint URL in the aud claim? This would require that the URL is a parameter in the Authorisation request, which doesn't seem ideal?*


{% include important.html content="In topologies where Consumer applications are provisioned via a portal or middleware hosted by another organisation (see [Topologies](ssp_topologies.html)), it is important for audit purposes that the user and organisation populated in the JWT reflect the originating organisation rather than the hosting organisation." %}

## Example Code ##

### C# ###

{% include tip.html content="The following code snippet utilise the [Microsoft Identity Model JWT Token Nuget Package](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/) for creating, serializing and validating JWT tokens." %}

TODO

### Java ###

TODO
