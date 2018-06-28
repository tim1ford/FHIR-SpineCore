---
title: Authorisation and Roles
keywords: legacy, security
tags: [legacy]
sidebar: foundations_sidebar
permalink: legacy_authorisation.html
summary: "This page provides an overview of the current approach for retrieving roles for applying RBAC controls"
---

{% include important.html content="The APIs described in this section are being replaced by [newer NHS Identity APIs](https://developer.nhs.uk/apis/national-authentication/), so implementers should be aware that these APIs will be replaced in future." %}

## Overview ##

The national RBAC model is described in more detail [here](security_rbac.html). This page describes the current process for applying RBAC controls in systems connecting to the Spine, and for systems that want to use national RBAC controls to manage access in local health and care IT systems.

In future, it will be possible to apply national RBAC controls through a national OAuth authorisation endpoint as part of the NHS Identity platform, but currently local systems are expected to apply appropriate RBAC controls. Where systems are accessing nationally held data or services on Spine, the assurance process will be used to ensure controls have been applied in systems before access will be granted.

## SSB SAML endpoint ##

The Spine exposes an API that can be used to retrieve the details of the authenticated user to support local authorisation decisions.

This can be accessed using a simple HTTP GET request to the role assertion URL. You can find the relevant URLs for each Spine environment in the relevant "authority service names" document - see the [evironments](test_environments.html) page for details:

```
HTTP GET https://[SSB Role assertion URL]/saml/RoleAssertion?token=[TOKEN_ID]
```

The TOKEN_ID above is the token returned from the "Get Ticket" API described on the [authentication page](smartcards.html) - this will need to be url encoded as it is likely to include characters that can't be put into a URL.

If the SSO session is active on the Spine, the response to the above should be a SAML assertion (a block of XML).

You can see a full example [here](saml_example.html).

The SAML assertion is a flat file that presents name/value pairs. When an attribute value is empty then no name/value pair will be displayed. The SAML assertion is formed in three parts:

 - Header block
 - Person block
 - Job Role Profile block or blocks

### Header Block ###

There will be one header block with a single attribute `ssbAssertionVersion` specifying the version of the assertion.

### Person Block ###

There will be one person block with the following name/value pairs:

| Name | LDAP Attribute (from [SDS](build_directory.html)) | Cardinality |
| ------------------------ | ----------------- | ---- |
| Full Name                | cn                | 1    |
| User's Unique Identifier | uid               | 1    |
| OCS Practitioner Code    | nhsOcsPrCode      | 0..1 |
| SSB Session Role Uid     | ssbSessionRoleUid | 1    |
| SSB Mode                 | ssbMode           | 0..1 |

- **Session Role**: At authentication time the user's session role is stored as a property ("SSB Session Role UID") in the SSO Token.  The "SSB Session Role UID" property value will be returned by the SAML assertion. The user’s session role may be obtained by obtaining an SSO Token then locally performing a getProperty() call against that SSO Token.  An alternate method would be to obtain a SAML assertion from the Spine and parse that assertion locally for the SSB Session Role UID.

- **SSB Mode**: The SSB Mode flag value is one of the following:
  - 1 (the digit one) – The user has opted to authenticate for Training only
  - 0 (the digit zero) – The user has opted to authenticate for regular Live (clinical care) mode only

### Job Role Profile Blocks ###

There will be a job role profile block for role profile assigned to the user by the registration agent. Information about each Job Role is taken from the [SDS directory](build_directory.html) "Organisation Person" and "Role Profile" entries for the user.

The hierarchical nature of Job Role profiles is represented in a flat file structure by combining attributes from each level of the hierarchy into a single name/value pair:

| Name                     | LDAP Attribute            | Cardinality |
| ------------------------ | ------------------------- | ----------- |
| NHS Organisation         | nhsIDCode                 | 1           |
| Organisation Name        | o                         | 1           |
| Job Role                 | nhsJobRole                | 1           |
| Areas of Work            | nhsAreaOfWork             | 0..*        |
| Work Groups              | nhsWorkGroups             | 0..*        |
| Areas of Work Codes      | nhsAreaOfWorkCodes        | 0..*        |
| Business Functions       | nhsBusinessFunctions      | 0..*        |
| Work Groups Codes        | nhsWorkGroupsCodes        | 0..*        |
| Job Role Code            | nhsJobRoleCode            | 1           |
| Role Profile Code        | uniqueIdentifier          | 1           |
| Business Functions Codes | nhsBusinessFunctionsCodes | 0..*        |

Notes:
- The order of attributes within the block is not important. Consuming applications should NOT be coded to take order into account. They should extract each role profile block and then extract the required attributes from it.
- The date format for nhsOrgOpenDate and nhsOrgCloseDate is YYYYMMDD
- An nhsPersonStatus <> 1 means that the person is inactive
- The presence of an nhsIDCode attribute (which is mandatory) can be used to determine the start of a Job Role Block. This is the key field to use when parsing the Job Roles structure into individual Job Role Blocks
- Workgroups are associated with individual Job Roles by parsing the Job Role structure; the nhsWorkGroupsCodes attribute (optional) shows which Work Groups the Job Role is a member of

## Passing User Context into National API calls ##

### Legacy Messaging ###

Legacy SOAP and ebXML messages to Spine are defined in the relevant MiM (Message Implementation Manual), and generally include fields to allow the user role ID (URPID) to be passed in the message. The specific fields to pass this in vary by message, and implementers should refer to the relevant [MiM release](https://developer.nhs.uk/library/interoperability/message-implementation-manuals-mims/) for details.

### FHIR APIs ###

All Spine FHIR APIs require that system and user context (where relevant) is passed into each call in the form of an Access token. For further details, see the [Access Tokens and Audit](security_jwt.html) section in this specification.

