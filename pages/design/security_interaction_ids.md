---
title: Spine Services and Interaction IDs
keywords: trust, pki, endpoints, interactions
tags: [design]
sidebar: overview_sidebar
permalink: security_interaction_ids.html
summary: "Details of how system-to-system authorisation is managed through the use of Interaction IDs for Services."
---

## Definitions ##

### Service Names ###

All services on the Spine are identified within Spine using a "Service Name". These service names take the form of a URN - for example:

- PDS:
  - ```urn:nhs:names:services:pds```
  - ```urn:nhs:names:services:pdsquery```
- SCR:
  - ```urn:nhs:names:services:psis```
  - ```urn:nhs:names:services:psisquery```

The service name identifies the high level type of interaction, and supports internal routing of messages in Spine.

Service names are also grouped into "Message Sets" to allow the Solutions Assurance team to provide test data tailored to the testing of specific services.

### Interaction IDs ###

Within each service, a number of interactions are typically defined. These are the more granular operations exposed by Spine services to clients - for example:

- Service: ```urn:nhs:names:services:pds``` - interactionIDs:
  - ```urn:nhs:names:services:pds:MCCI_IN010000UK13``` Application Acknowledgement
  - ```urn:nhs:names:services:pds:PRPA_IN000201UK01``` PDS NHS Number Allocation Request Accepted
  - ```urn:nhs:names:services:pds:PRPA_IN000202UK01``` PDS General Update Request Accepted
  - ```urn:nhs:names:services:pds:PRPA_IN000209UK02``` PDS Primary Care Update Request Accepted
  - ```urn:nhs:names:services:pds:PRPA_IN000210UK01``` PDS Log GP2GP Record Transfer Request
  - ```urn:nhs:names:services:pds:PRPA_IN000211UK01``` PDS Log GP2GP Record Transfer Request Accepted
  - ```urn:nhs:names:services:pds:PRPA_IN000212UK02``` PDS Primary Care NHS Number Allocation Request
  - etc...

These interaction IDs are used during the assurance process to identify which interactions a system has been accredited to perform.

## Naming for FHIR Interaction IDs ##

The legacy messaging interfaces designed in Spine 1 used the interactions defined in the [MiM](mim_messaging.html) as the identifier for all HL7v3 interactions.

Newer APIs have adopted FHIR, and a new naming strategy has been defined for the interaction IDs for these APIs.

Service names will remain in the same format - e.g.:

- Visitors and Migrants: ```urn:nhs:names:services:visitorsandmigrants```
- Reasonable Adjustment Flagging: ```urn:nhs:names:services:flagserver```
- Subscriptions: ```urn:nhs:names:services:subscriptions```

However, the interaction IDs will be based around FHIR resources, and will take a similar form to the structure of SMART on FHIR scopes (see [security scopes](security_scopes.html)). The structure will be:

<!-- Diagram generated using: http://www.bottlecaps.de/rr/ui -->

```interactionID ::= 'urn:nhs:names:services:' service ':' (fhir-resource | extended-operation-name) '.' ("read" | "write") ':' major-version```

![Interaction ID Grammar](images/design/interactionID.png)

- ```fhir-resource``` is the name of a standard FHIR resource as defined in the [HL7 FHIR specification](https://www.hl7.org/fhir/resourcelist.html)
- ```extended-operation-name``` is used to identify a custom FHIR extended operation name (see [FHIR Extended Operations](https://www.hl7.org/fhir/operations.html))
- ```read``` is used to identify that the operation **does not** alter the state of any resources (usually a READ or SEARCH operation)
- ```write``` is used to identify that the operation **does** alter the state of one or more resources (usually a CREATE, UPDATE or DELETE operation)
- ```major-version``` is used to identify the major version number for the service. This allows the Spine to continue supporting clients using previous versions of an API for a period of time when a new major version is released.

### Examples ###

Examples of the above Interaction ID grammar are:

- Visitors and Migrants:
  - ```urn:nhs:names:services:visitorsandmigrants:Observation.read:1``` - Read V&M Observation Resources
- Reasonable Adjustment Flagging:
  - ```urn:nhs:names:services:flagserver:Consent.read:1``` - Read/Search Flagging Consent Resources
  - ```urn:nhs:names:services:flagserver:Consent.write:1``` - Update/Delete Flagging Consent Resources
  - ```urn:nhs:names:services:flagserver:Flag.read:1``` - Read/Search Flagging Flag Resources
  - ```urn:nhs:names:services:flagserver:Flag.write:1``` - Update/Delete Flagging Flag Resources

Please refer to the relevant API spec for the specific interaction IDs to use for each Spine service.

## Identifying Interactions in Spine Requests ##

In order to both route requests within Spine, and to verify that the ASID of the requesting system relates to a system that has been accredited to perform that interaction, all requests MUST specify the interaction they are performing in a HTTP header.

For FHIR APIs, this will be passed in the ```InteractionID``` header (see [headers](resources_headers.html))

