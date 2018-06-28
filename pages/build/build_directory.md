---
title: Directory Service (SDS)
keywords: build, directory
tags: [design]
sidebar: overview_sidebar
permalink: build_directory.html
summary: "The Spine Directory Service provides details of organisations, people and systems registered with the Spine"
---

{% include important.html content="All information provided below is indicative and subject to on-going review." %}

# Directory Structure #

The Spine Directory is a simple LDAP directory with the following basic structure:

- O=nhs
	- ou=Organisations
	- ou=People
	- ou=Services
	- ou=ReferenceData
	- ou=ChangeLog

The information contained in each branch is:

## Organisations ##

Data about the organisational structure of the NHS - taken from the Organisation Data Service (note, these are the "legal" organisations rather than business services available to patients). The directory on Spine contains the following types of organisation entity:

- ou=Organisations
	- Organisation (nhsOrg)
	- Organisation Site
	- Department

Typically, consumers are likely to find it easier to consume organisation data from the [ODS FHIR API](https://developer.nhs.uk/apis/ods/).

## People ##

The people branch contains data about all the users of the Spine. This data is confirmed by the Registration Authority during the user registration process and issuance of the user’s smartcard. The structure of the People branch is as follows:

- ou=People
	- Person (nhsPerson)
		- Organisational Person (nhsOrgPerson)
			- Role Profile (nhsOrgPersonRole)

Each Spine user has one entry immediately beneath the people branch. This entry contains all the data that is independent of an organisation e.g. first and family names. 

Immediately beneath a person entry are organisational person entries associated with the user’s specific organisation data e.g. organisation name. A user may have multiple organisational person entries, one for each organisation with which they have a direct association.

Located beneath each specific organisational person entry are entries for each role profile that a user holds within the organisation. These role profile entries contain details of the job role, area of work, work groups and business functions associated with that role profile.

## Services ##

The services branch contains data about Accredited Systems and Message Handling Systems registered with the Spine. The structure of the Services branch is as follows:

- ou=Services
	- Accredited System (nhsAs)
	- Message Handling System (nhsMhs)

The accredited system entry type contains all the information related to a specific system: 

- the accredited system identifier
- the party key of the Message Handling System to which messages for the accredited system should be sent
- the service-interactions the system supports, including versions

The message handling system entry type contains all the information related to a specific service-interaction supported by the system:

- the party key of the message handling system
- the version of the service-interaction the system supports
- the end-point bindings
- the interaction contract properties

## ReferenceData ##

The Reference Data branch contains five sub-branches:

- ou=ReferenceData
	- Types
	- Sub‑Types
	- Category Bags
	- Role Base Access Control entries
	- Work Groups
	- Work Group Types

The Types and Sub-Types contain types of Organisations, Sites and Departments referenced in the Organisation branch. Typically consumers will find it simpler to use the [ODS FHIR API](https://developer.nhs.uk/apis/ods/) for this.

Category Bags are not typically used for FHIR APIs.

Role Base Access Control entries contain the national "baseline" RBAC roles and the business activities associated with each role. Each baseline entry includes:

- Baseline Code
- Job Role Code and Name
- A list of Area of Work Codes and Names
- A list of Business Function codes
- DN of the URP (User Role Profile) that created the baseline
- Creation Date

Work Groups and Work Group Types were used by the national Legitimate Relationship service, but are not typically used for FHIR APIs.

## ChangeLog ##

Change Log entries are used to allow external directories to be synchronised with changes made to the Spine Directory.


