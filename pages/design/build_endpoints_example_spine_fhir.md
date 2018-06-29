---
title: Endpoint Lookup Example - Spine FHIR Endpoints
keywords: build, directory, endpoints
tags: [design]
sidebar: overview_sidebar
permalink: build_endpoints_example_spine_fhir.html
summary: "Worked example of looking up a Spine endpoint from SDS for Spine FHIR APIs"
---

## Worked example of the endpoint lookup process ##

### Step 0. PDS Trace (pre-requisite step)

The Consuming system is responsible for [performing a PDS Trace](pds_overview.html) to verify the identity of the patient and find or confirm their NHS Number.

For this example, NHS Number 9000000084 with demographic details Mr Anthony Tester, 19 Ficticious Avenue, Testtown

### Step 1. Accredited System Lookup on SDS

Assuming we are using the Spine Development environment, we can refer to the [details of that environment](test_environments.html) and use the fixed party key value: **YES-0000806**

	
### Step 2: MHS lookup on SDS to determine FHIR endpoint Server Root URL

We need to identify the interaction ID appropriate for the API call we wish to make. Assuming we are calling the Visitors and Migrants API and want to use major version 1 of that API, the interaction ID we need (as per the [API spec](https://developer.nhs.uk/apis/vandm) for the API) is: **urn:nhs:names:services:visitorsandmigrants:fhir:rest:search:observation:1**

Using the Party Key identified in Step 1, and the interaction ID, the following ldapsearch query is executed against the SDS URL for the appropriate [environment](test_environments.html):

	ldapsearch -x -H ldaps://ldap.vn03.national.ncrs.nhs.uk -b "ou=services, o=nhs" 
	"(&(nhsMhsPartyKey=YES-0000806) (objectClass=nhsMhs) (nhsMhsSvcIA=urn:nhs:names:services:visitorsandmigrants:fhir:rest:search:observation:1))" 
	nhsMhsEndPoint nhsMHSFQDN
	

This query should return a single endpoint. In this case, the ldapquery returns the following results:

TODO
