---
title: Personal Demographic Service
keywords: spine, pds, integration, patient, demographics
tags: [design]
sidebar: overview_sidebar
permalink: pds_overview.html
summary: "Overview of the role of the Personal Demographic Services (PDS) and ways of accessing it."
---

## PDS Tracing ##

All national APIs that provide data about specific patients require that consuming systems MUST obtain a verified NHS number from the [Personal Demographic Service](https://digital.nhs.uk/Demographics){:target="_blank"} (PDS) to ensure they have correctly identified the patient. This typically involves "tracing" patients using key information such as their Date of Birth, Surname and Gender.

In future, PDS tracing will be available through a FHIR API, but at present, there are three ways of carrying out this trace process:

### 1. Full PDS Spine compliant system (PDS) ###

Consumer systems MAY follow the guiding principles of [how systems should integrate with PDS](http://webarchive.nationalarchives.gov.uk/20160921135209/http://systems.digital.nhs.uk/demographics/spineconnect). This describes in particular the principles governing how systems should synchronise with PDS as the master repository of demographics data to ensure local system data does not become stale.

There is a very limited risk that the consumer-traced NHS number pre-dates a patient record status change to sensitive Flag (S-Flag) immediately prior to use.

### 2. Spine Mini Service PDS (SMSP) ###

Consumer systems MAY utilise the [Spine Mini Service Provider for PDS](https://developer.nhs.uk/library/systems/nhs-digital-smsp-pds/){:target="_blank"} (SMSP) as a lighter weight alternative to integrating with the full Spine Personal Demographic Service (PDS).

Other accredited commercial Spine "Mini Service" providers can also be used. Details of suppliers that can offer accredited Spine Mini Services for PDS can be found on the [ITK accreditation catalogue](https://digital.nhs.uk/interoperability-toolkit/accreditation-catalogue)

{% include important.html content="As the SMSP service does not return multiple possible matches for the patient it is typically only suitable to be used where there is enough information to achieve a single matched trace." %}

{% include note.html content="SMSP responses do not include trace sequence number information. The presence of a trace sequence number in a consumer request to an API provider system could enable providers to programmatically determine the need to perform a PDS trace themselves, for example if their local patient record's trace was not current." %}

### Demographics Batch Service (DBS) ###

Consumers MAY utilise the [Demographics Batch Service (DBS)](http://developer.nhs.uk/library/systems/demographic-batch-service-dbs/){:target="_blank"} to perform batch PDS Tracing.

The suitability of the use of DBS to perform batch PDS tracing would be assessed as part of the Consumer Accreditation process.

