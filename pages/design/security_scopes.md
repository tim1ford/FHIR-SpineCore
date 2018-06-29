---
title: Using Scopes for Authorisation
keywords: security, authorisation, openid, oauth, scopes
tags: [design]
sidebar: overview_sidebar
permalink: security_scopes.html
summary: "Use of Scopes in Authorisation Requests and links to the national RBAC model."
---

As part of the roadmap for the new [NHS Identity](https://developer.nhs.uk/apis/national-authentication/) services, there is an intention to move to using the OAuth standard to provide authorisation for access to national services using a flexible set of "scopes". The aim it is to align as closely as possible to the [SMART on FHIR](http://docs.smarthealthit.org/) standard for these scopes to make it easy for existing providers of systems that implement SMART on FHIR to make use of national systems, and for those using national services to authenticate and authorise access to systems (including local systems). It also provides a more standard mechanism for authorising access to FHIR resources across all services going forwards.

The specific scopes that will be used across each API are still being determined, but the below table gives examples of the scopes defined as part of the [GP Connect](https://developer.nhs.uk/apis/gpconnect/) project:

| Endpoint      | Acceptable values of requested_scope JWT claim |
| ------------- | -----------------------------------|
| /Patient      | patient/*.[read/write] |
| /Organization | organization/*.[read/write] |
| /Appointment  |patient/*.[read/write] |
| /Task         | organization/*.[read/write] |
| /Practitioner | organization/*.[read/write] |
| /Location     | organization/*.[read/write] |
| /Slot         | organization/*.[read/write] |

The wider work on [CareConnect APIs](https://nhsconnect.github.io/CareConnectAPI/) has also defined a similar scope structure for access to FHIR resources through CareConnect compliant APIs:

| Endpoint             | Acceptable values of requested_scope JWT claim |
| -------------------- |----------------|
| /Patient             | user/Patient.read |
| /Observation         | user/Observation.read |
| /Encounter           | user/Encounter.read |
| /Condition           | user/Condition.read |
| /Procedure           | user/Observation.read |
| /AllergyIntolerance  | user/AllergyIntolerance.read |
| /MedicationRequest   | user/MedicationPrescription.read |
| /MedicationStatement | user/MedicationStatement.read |
| /Immunization        | user/Immunization.read |
| /DocumentReference   | user/DocumentReference.read |
| /Binary              | user/Binary.read |

Further work is needed to ensure these resource-level scopes can map sensibly to RBAC roles and activities.

