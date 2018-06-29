---
title: Foundation | Operation Outcome
keywords: operationoutcome
tags: [foundation]
sidebar: overview_sidebar
permalink: api_spine-operationoutcome.html
summary: A collection of error, warning or information messages that result from a system action.
---

{% include custom/fhir.referencemin.html resource="[Spine-OperationOutcome](https://fhir.nhs.uk/STU3/StructureDefinition/Spine-OperationOutcome-1)" page="" fhirlink="[Operation Outcome](https://www.hl7.org/fhir/operationoutcome.html)" content="User Stories" userlink="" %}


## Spine Operation Outcome ##

The OperationOutcome resource provides information back in response to an API call for any error, warning and information messages which may need to be returned - usually in place of whatever output the client may have expected.

The most common use-case for this is to provide error messages back when an operation has failed for some reason. See the [error handling page](resources_error_handling.html) for details of specific error and warning responses that may be returned.
