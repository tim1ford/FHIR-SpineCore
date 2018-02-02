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

API Provider systems SHALL respond to oAuth Bearer Token errors inline with [RFC 6750 - section 3.1](https://tools.ietf.org/html/rfc6750#section-3.1).

It is highly recommended that standard libraries are used for creating the JWT as constructing and encoding the token manually may lead to issues with parsing the token. A good source of information about JWT and libraries to use can be found on the [JWT.io site](https://jwt.io/)


### JWT Generation ###

Consumer system SHALL generate a new JWT for each API request. The consumer generated JSON Web Token (JWT) SHALL consisting of three parts seperated by dots `.`, which are:

- Header
- Payload
- Signature

#### Header ####
Consumer systems SHALL generate an Unsecured JSON Web Token (JWT) using the 'none' algorithm parameter in the header to indicate that no digital signature or MAC has been performed (please refer to section 6 of [RFC 7519](https://tools.ietf.org/html/rfc7519){:target="_blank"} for details).

```json
{
  "alg": "none",
  "typ": "JWT"
}
```

#### Payload ####

Consumers systems SHALL generate a JWT Payload conforming to the requiremnts setout in the [JWT Payload](#jwt-payload) section of this page.

#### Signature ####

Consumer systems SHALL generate an empty signature.

#### Complete JWT ####

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
| sub | R | ID for the user on whose behalf this request is being made. Will match wither the `requesting_user` or `requesting_patient` | No | Yes |
| aud | R | Requested resource URI<sup>1</sup> | No | Yes |
| exp | R | Expiration time integer after which this authorization MUST be considered invalid. | No | (now + 5 minutes) UTC time in seconds |
| iat | R | The UTC time the JWT was created by the requesting system | No | now UTC time in seconds |
| reason_for_request | R | Purpose for which access is being requested | `directcare` | No |
| requested_scope | R | Data being requested | `patient/*.[read|write]` <br/>OR <br/>`organization/*.[read|write]` | No |
| requesting_device | R | Identifier for the system or device making the request | No | System or Device Identifier |
| requesting_organization | R | Organisation making the request | No | Organisation Identifier | 
| requesting_user | R | Health or Social Care professional making the request | No | User Identifier |
| requesting_patient | R | Citizen making the request | No | NHS Number |




<sup>1</sup> The URI for the requested resource, including the fully qualified endpoint address returned to the Consumer by the [SDS endpoint lookup service](https://nhsconnect.github.io/gpconnect/integration_spine_directory_service.html#worked-example-of-the-endpoint-lookup-process){:target="_blank"} as the value of `nhsMhsEndPoint`.

<sup>2</sup> Minimal FHIR resource to include any relevant business identifier(s), conforming to the base STU3 FHIR resources definition (the resource does not need to conform to the GP Connect FHIR resource profile).

<sup>3</sup> The `requesting_organization` **SHALL** refer to the care organisation from where the request originates.

<sup>4</sup> To contain the practitioners local system identifier(s) (i.e. login details / username). Where the user has both a local system 'role' as well as a nationally-recognised role, then the latter SHALL be provided. Default usernames (e.g. referring to systems or groups) SHALL NOT be used in this field.


{% include important.html content="In topologies where Consumer applications are provisioned via a portal or middleware hosted by another organisation (see [Topologies](ssp_topologies.html)), it is important for audit purposes that the user and organisation populated in the JWT reflect the originating organisation rather than the hosting organisation." %}

### Population of requesting_organization ###

The `consumer` SHALL populate the `requesting_organization` claim with:

* A FHIR [Organization](https://www.hl7.org/fhir/STU3/organization.html) ![STU3](images/stu3.png) resource representing the organization making the request and SHALL include the elements:

  | Element | Description |
  | --- | --- |
  | name | A textual representation of the name of the organisation. |
  | identifier | An identifier should be included contain a fixed `system` of `"https://fhir.nhs.uk/Id/ods-organization-code"` and a identifier `value` containing the ODS code of requesting organsiation. |

  <div class="deprecated">
  <h3>Deprecated - required to support AccessRecord 1.0.0-rc.5</h3>
  <p>For backward compatablity with consumers still using an implementation of GP Connect based on the previous version of the specification, 'providers' SHALL support requests where the 'requesting_organization' conforms to the requirements above but also request where the 'requesting_organization' is populated with:</p>

  <p>A FHIR <a href="https://www.hl7.org/fhir/DSTU2/organization.html">Organization</a> <img src="images/dstu2.png" /> resource representing the organization making the request and SHALL include the elements:</p>
  <table>
	<tr>
		<th>Element</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>name</td>
		<td>A textual representation of the name of the organisation.</td>
	</tr>
	<tr>
		<td>identifier</td>
		<td>An identifier should be included contain a fixed 'system' of "http://fhir.nhs.net/Id/ods-organization-code" and a identifier 'value' containing the ODS code of requesting organsiation.</td>
	</tr>
  </table>

  {% include important.html content="Use of the FHIR [Organization](https://www.hl7.org/fhir/DSTU2/organization.html) ![DSTU2](images/dstu2.png) resource and '`http://fhir.nhs.net/Id/ods-organization-code`' identifier system is being deprecated and should not be used by Consumers when implementing this version of the specification. Once all Consumers have migrated to use the new FHIR resource and identifier system, support for the deprecated format will be removed from the specification." %}
  </div>

### Population of requested_record ###

The `consumer` SHALL populate the `requested_record` claim with:

* Either a FHIR [Organization](https://www.hl7.org/fhir/STU3/organization.html) ![STU3](images/stu3.png) resource or a FHIR [Patient](https://www.hl7.org/fhir/STU3/patient.html) ![STU3](images/stu3.png) resource which describes the resource being requested or searched for, where possible, and will contain any relevant business identifiers for the request.

  The table below shows some of the GP Connect API interactions and the expected content for the JWT requested_record claim:

  | Request | Known Business Identifiers | requested_record Resource Type | requested_record Content |
  | --- | --- | --- | --- |
  | Access Record HTML | NHS Number | Patient | SHALL contain an identifier element containing the NHS Number being passed as a parameter to the `gpc.getcarerecord` operation. |
  | Patient Search | NHS Number | Patient | SHALL contain an identifier element containing the NHS Number being passed as the search parameter of the request. |
  | Patient Read | N/A | Patient | SHOULD contain the logical id of the patient resource being requested. |
  | Organization Search | ODS Code | Organization | SHALL contain an identifier element containing the organization ODS Code which is being passed as the parameter to the search. |
  | Practitioner Search | N/A | Organization | SHOULD contain any relevant identifier for the organization on which the FHIR endpoint resides. |
  | Practitioner Read | N/A | Organization | SHOULD contain any relevant identifier for the organization on which the FHIR endpoint resides. |
  | Search for free slots | N/A | Organization | SHOULD contain any relevant identifier for the organization on which the FHIR endpoint resides. |
  | Book Appointment | NHS Number | Patient | The consumer will have previously used the patients NHS Number to find the patients logical id on the providers system, therefore the requested_record Patient SHOULD contain the NHS number identifier element. |
  | Read Appointment | NHS Number | Patient | The consumer will have previously used the patients NHS Number to find the patients logical id on the providers system, therefore the requested_record Patient SHOULD contain the NHS number identifier element. |
  
  The following identifier sytems SHALL be used when populating the relevant business identifiers:
  
  | Known Business Identifiers | Relavant Business Identifiers |
  | --- | --- |
  | NHS Number | https://fhir.nhs.uk/Id/nhs-number |
  | ODS Code | https://fhir.nhs.uk/Id/ods-organization-code |

  {% include note.html content="The provider SHALL validate that the requested_record claim details match the request parameters where possible, to ensure valid auditing of the requests end-to-end." %}

  <div class="deprecated">
  <h3>Deprecated - required to support AccessRecord 1.0.0-rc.5</h3> 
  <p>For backward compatablity with consumers still using an implementation of GP Connect based on the previous version of the specification, 'providers' SHALL support requests where the 'requested_record' conforms to the requirements above but also request where the 'requested_record' is populated with:</p>

  <p>Either a FHIR <a href="https://www.hl7.org/fhir/DSTU2/organization.html">Organization</a> <img src="images/dstu2.png" /> resource or a FHIR <a href="https://www.hl7.org/fhir/DSTU2/patient.html">Patient</a> <img src="images/dstu2.png" /> resource which describes the resource being requested or searched for, where possible, and will contain any relevant business identifiers as set out by the table above.</p>
  
  <p>The following identifier sytems SHALL be supported within the resources for the relevant business identifiers:</p>
  <table>
	<tr>
		<th>Known Business Identifiers</th>
		<th>Relavant Business Identifiers</th>
	</tr>
	<tr>
		<td>NHS Number</td>
		<td>http://fhir.nhs.net/Id/nhs-number</td>
	</tr>
	<tr>
		<td>ODS Code</td>
		<td>http://fhir.nhs.net/Id/ods-organization-code</td>
	</tr>
  </table>

  {% include important.html content="Use of the FHIR [Organization](https://www.hl7.org/fhir/DSTU2/organization.html) ![DSTU2](images/dstu2.png) resource, the FHIR [Patient](https://www.hl7.org/fhir/DSTU2/patient.html) ![DSTU2](images/dstu2.png) resource, the 'http://fhir.nhs.net/Id/nhs-number' identifier system and the 'http://fhir.nhs.net/Id/ods-organization-code' identifier system is being deprecated and should not be used by Consumers when implementing this version of the specification. Once all Consumers have migrated to use the new FHIR resource and identifier system, support for the deprecated format will be removed from the specification." %}
  </div>
  

### Population of requested_device ###

This claim is used to provide details of the originator of the request for auditing purposes, in the form of a FHIR Device resource. 

Where the request originates from a device (for example a mobile device in a patient facing scenario), details of the device can be provided in manufacture, model and version elements.

Where the request originates from a system, the Spine endpoint url of the originating system shall be specified using the url element.

### Population of iss claim ###

As the consuming system is presently responsible for generating the access token, this SHALL contain the url of the Spine endpoint of the consuming system.

In future OAuth2 implementation, the iss claim will contain the url of the OAuth2 authorisation server token endpoint.


### JWT Payload Example ###

```json
{
	"iss": "https://[ConsumerSystemURL]",
	"sub": "https://fhir.nhs.uk/Id/sds-role-profile-id"|[SDSRoleProfileID]",
	"aud": "https://provider.thirdparty.nhs.uk/GP0001/STU3/1",
	"exp": 1469436987,
	"iat": 1469436687,
	"reason_for_request": "directcare",
	"requested_scope": "patient/*.read",
	"requesting_device": "https://fhir.nhs.uk/Id/accredited-system|[ASID]",
	"requesting_organization": "https://fhir.nhs.uk/Id/ods-organization-code|[ODSCode]",
	"requesting_practitioner": "https://fhir.nhs.uk/Id/sds-role-profile-id"|[SDSRoleProfileID]"
}
```

Where the Practitioner has both a local system identifier but no Spine identifier, then the a local identifier MAY be used with a suitable URI to identify the system/organisation that assigned the ID.


## Example Code ##

### C# ###

{% include tip.html content="The following code snippet utilise the [Microsoft Identity Model JWT Token Nuget Package](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/) for creating, serializing and validating JWT tokens." %}

```C#
var requesting_device = new Device {
	Id = "[DeviceID]",
	Model = "[SoftwareName]",
	Version = "[SoftwareVersion]",
	Identifier =
	{
		new Identifier("[DeviceSystem]", "[DeviceID]")
	}
};

var requesting_organization = new Organization {
	Id = "[OrganizationID]",
	Name = "Requesting Organisation Name",
	Identifier =
	{
		new Identifier("https://fhir.nhs.uk/Id/ods-organization-code", "[ODSCode]")
	}
};

var requesting_identity = new Practitioner {
	resourceType = "Practitioner",
	Id = "[PractitionerID]",
	PractitionerRole =
	{
		new role()
		{
			new coding("http://fhir.nhs.net/ValueSet/sds-job-role-name-1", "[SDSJobRoleName]")
		}
	},
	Name = new HumanName()
	{
			Prefix = new[] {"[Prefix]"},
			Given = new[] {"[Given]"},
			Family = new[] {"[Family]"}
	},
	Identifier =
	{
		new Identifier("http://fhir.nhs.net/sds-user-id", "[SDSUserID]"),
		new Identifier("[UserSystem]", "[UserID]")
	}
};

var subject_patient = new Patient {
	Identifier =
	{
		new Identifier("https://fhir.nhs.uk/Id/nhs-number","[NHSNumber]")
	}
};

var audit_event_id = "[AuditEventID]";
var requesting_system_url = "https://[ConsumerSystemURL]";
var requesting_system_token_url = "https://authorize.fhir.nhs.net/token";

// --this example getting local patient ID 1 at gp practice GP001
var target_request_url = "https://http://gpconnect.aprovider.nhs.net/GP0001/DSTU2/1/Patient/1";
var now = DateTime.UtcNow;
var expires = now.AddMinutes(5);

var claims = new List<System.Security.Claims.Claim>
{
    new System.Security.Claims.Claim("iss", requesting_system_url, ClaimValueTypes.String),
    new System.Security.Claims.Claim("sub", requesting_practitioner.Id, ClaimValueTypes.String),
    new System.Security.Claims.Claim("aud", target_request_url, ClaimValueTypes.String),
    new System.Security.Claims.Claim("exp", EpochTime.GetIntDate(expires).ToString(), ClaimValueTypes.Integer64),
    new System.Security.Claims.Claim("iat", EpochTime.GetIntDate(now).ToString(), ClaimValueTypes.Integer64),
    new System.Security.Claims.Claim("reason_for_request", "directcare", ClaimValueTypes.String),
    new System.Security.Claims.Claim("requested_scope", "patient/*.read", ClaimValueTypes.String),	
    new System.Security.Claims.Claim("requesting_device", FhirSerializer.SerializeToJson(requesting_device), JsonClaimValueTypes.Json),
    new System.Security.Claims.Claim("requesting_organization", FhirSerializer.SerializeToJson(requesting_organization), JsonClaimValueTypes.Json),
    new System.Security.Claims.Claim("requesting_identity", FhirSerializer.SerializeToJson(requesting_identity), JsonClaimValueTypes.Json)
};

// Serialize To Json
JwtPayload payload = new JwtPayload(claims);
var jsonPayload = payload.SerializeToJson();
jsonPayload.Dump();

```

## External Documents / Policy Documents ##

| Name | Author | Version | Updated |
| GPSoC IG Requirements for GP Systems | NHS Digital | v4.0 | 19/09/2014 |
| GP Systems Interface Mechanism Requirements V 1| NHS Digital | v0.10|
