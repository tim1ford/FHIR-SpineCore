---
title: Registering a provider endpoint
keywords: build, directory, endpoints
tags: [design]
sidebar: overview_sidebar
permalink: ssp_providers.html
summary: "Requirements for systems wanting to register an API endpoint for consumers to call through the SSP"
---

{% include important.html content="All information provided below is indicative and subject to on-going review." %}

{% include note.html content="Most of the below currently relates purely to GP Connect APIs as these are the initial APIs being brokered through the SSP. As other CareConnect APIs are implemented, it is intended that these will also be made available through the SSP, at which point this page may be updated with more information to reflect this wider use." %}

## Provider System Requirements ##

For systems providing APIs to be accessed through the [Spine Secure Proxy](ssp_overview.html), the provider system is responsible for populating SDS with the necessary information to enable reliable endpoint lookup by consuming systems.

In order to ensure that endpoint lookup is reliable, the following guidelines must be followed for First of Type implementations:

### 1. Format of Server Root URL

The *Server Root URL* for a given ASID SHALL be defined in the nhsMhsEndPoint attribute of the MHS record (i.e. the ldap object of type nhsMhs). For GP Connect endpoints this URL SHALL be in the format described in the GP Connect [API Versioning](https://developer.nhs.uk/apis/gpconnect/development_general_api_guidance.html#service-root-url-versioning) guidance.


### 2. For First of Type interactions, CMA type endpoints only will be used

A CMA type endpoint refers to an endpoint which is a combined MHS sytem and Accredited System endpoint. There will be a 1-1 mapping between an Accredited System (uniquely identified by an ASID) and a Message Handling System (MSH). A single MHS record SHALL be associated with a given ASID and interaction ID.

### 3. nhsMhsEndPoint attribute SHALL contain the FHIR Server Root URL only

The nhsMHsEndPoint attribute SHALL contain the *FHIR Server Root URL*. It is the responsibility of the consuming system to construct the FHIR operation or RESTful resource request which will be postfixed to this base URL.

I.e. an example of a FHIR server root URL for a GetCareRecord interaction at practice GP0001 would be

`https://provider.thirdparty.nhs.uk/GP0001/DSTU2/1`

Note that the "Patient/$gpc.getcarerecord" is not added.

In line with this, provider systems SHOULD perform checks that the FHIR request received is a reasonable means to request the resource in view given the specificed interaction. 


### 4. Practice routing identifier to be included in FHIR Server Root URL

For GP Connect APIs, as described in the GP Connect [API Versioning](https://developer.nhs.uk/apis/gpconnect/development_general_api_guidance.html#service-root-url-versioning) guidance, a routing identifier SHALL be placed in the FHIR Servder Root URL. This routing identifier may be the ODS code of the practice, or another logical identifier which acheives reliable routing of the request to the patient's registered practice data store. It is expected that the FHIR server business logic will extract the routing identifier.

In line with this, HTTP headers SHALL NOT be used to provide this organisation routing.

### 5. Practice specific ODS codes to be used

ODS codes which refer to Principle Clinical Systems as a single entity SHALL NOT be used to provide routing. Practice specific ODS codes SHALL be used for routing purposes in the FHIR Server Root URL found in the NhsMhsEndPoint attribute of the MHS record.


### 6. The FHIR Server Root URL SHALL contain the FHIR version name

For GP Connect APIs, the FHIR Server Root URL defined in the nhsMhsEndPoint attribute SHALL contain the FHIR version name as described in the GP Connect [API Versioning](https://developer.nhs.uk/apis/gpconnect/development_general_api_guidance.html#service-root-url-versioning) guidance. This will enable versioning of provider API by FHIR version. 

In line with this, provider systems SHALL NOT version through the use of HTTP headers.


### 7. FHIR version SHALL match version found in FHIR conformance statement

The FHIR version as returned in a [CapabilityStatement](api_foundation_capabilitystatement.html) from the FHIR server which services the FHIR request SHALL match the FHIR version given in the FHIR Server Root URL. Details of how to retrieve this for GP Connect FHIR servers can be found at [GP Connect - Get the FHIR capability statement](https://developer.nhs.uk/apis/gpconnect/foundations_use_case_get_the_fhir_capability_statement.html)

### 8. FHIR Server Root URLs associated with a given product set SHALL use same FHIR Version

Where a provider moves in future to later version of FHIR, it will be necessary to define a new product set to accommodate the set of interactions provided by this. FHIR Server Root URLs defined for a specific product set SHALL all reference the same FHIR version. This ensures that FHIR resources references returned in FHIR responses are locally resolvable. 

For example, all interactions associated with the Appointment Management capability in a given product set must refer to the same FHIR server, so that the resource references for "Read Appointment" and "Amend" appointment would be locally resolvable to the same resource on the same FHIR Server. 


### 9. Acceptable use of ASID information in HTTP Headers

Source and destination ASID information is passed to the provider system from the Spine Security Proxy. Providers SHALL use this information for audit and debugging purposes only, and SHALL NOT use these headers to perform routing or lookups. 

It is the responsibility of the Spine Security Proxy SSP to perform lookups to determine consumer accreditation status. Routing shall be carried out as described above through practice specific ODS codes present in the FHIR Server Root URL. 


