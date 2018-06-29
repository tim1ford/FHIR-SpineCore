---
title: Visitors and Migrants Integration Example
keywords: pds, ssp, sds, integration, visitors, migrants
tags: [design]
sidebar: overview_sidebar
permalink: integration_example_vandm.html
summary: "Illustration of the use of the Visitors and Migrants API showing all interactions required with Spine services."
---

## Spine integration required to retrieve visitors and migrants chargeable status ##

The full details of the visitors and migrants APIs can be found in the [Visitors and Migrants API Specification](https://developer.nhs.uk/apis/vandm){:target="_blank"}.

Consumers of the visitors and migrants API are required to integrate with the following Spine services as a pre-requisite to making API calls:

- [Personal Demographics Service (PDS)](integration_personal_demographic_service.html)
- [Spine Directory Service (SDS)](integration_spine_directory_service.html)

To illustrate this, an example is given below of all the steps required consume V&M data. For full details, please refer to the relevant spine service pages.

## Example: Find a patient and retrieve their chargeable status ##

The following sequence diagram illustrates all the steps which a Consumer system would be required to undertake in order to query the Spine for the chargeable status of a patient:

![Sequence diagram for querying chargeable status](images/integration/vandm_sequence_diagram.gif)

The steps shown in the diagram are detailed below.

| Step | Description |
|------|-------------|
|      | *Step 1 is optional in the sense that a cached version of a previous these trace results may be available to the consumer.* |    
| 1a   | **Consumer** is responsible for performing a  [Personal Demographics Service(PDS)](pds_overview.html) Trace to both verify the NHS Number and confirm that they have the correct patient demograhic details. |
| 1b   | **PDS** returns the verified NHS Number. |
|      |      |
|      | *Step 2 is optional in the sense that cached endpoint details for the Spine FHIR endponit may be available from a previous SDS call.* |    
| 2a   | **Consumer** calls [Spine Directory Service (SDS)](build_endpoints_example_spine_fhir.html) to retrieve the FHIR endpoint URL. |
| 2b   | **SDS** returns the MHS entry containing the FHIR endpoint URL. | 
|      |      |
| 3a   | **Consumer** then makes an API call to [Search for Chargeable Status](https://developer.nhs.uk/apis/vandm/foundations_use_case_chargeable_status_indicator.html){:target="_blank"} for the patient. |
| 3b   | **Spine** returns the FHIR Observation that contains the chargeable status information. |

