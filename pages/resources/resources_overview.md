---
title: Resources Overview
keywords: getcarerecord, structured, rest, resource
tags: [rest,fhir,api]
sidebar: foundations_sidebar
permalink: explore.html
summary: "Overview of the Resources section"
---

{% include custom/search.warnbanner.html %}

## 1. Pre-Requisites ##

The [general policies](https://developer.nhs.uk/apis/fhir-policy) on the use and implementation of national FHIR APIs MUST be followed.

## 2. Resource API Structure ##

The FHIR Resources described in the Explore section of this implementation guide have been structured consistently in the following way:
- `0.` References
- `1.` Read
- `2.` Search Parameters
- `3.` Example

### 2.1 Resource API Structure Details ###

<table style="min-width:100%;width:100%">
<tr id="clinical">
<th style="width:20%;">General</th>
<th style="width:80%;">Description </th>
</tr>
<tr>
<td>0. References</td>
<td>Links to other parts of the implementation guide which might help with context and understanding the API's described</td>
</tr>
<tr>
<td>1. Read</td>
<td>A description of how to get the API</td>
</tr>
<tr>
<td>2. Search Parameters</td>
<td>List of search parameters for the profile being described, including any tips for searching. This section shows examples of how to search using the provided search parameters</td>
</tr>
<tr>
<td>3. Example</td>
<td>Description of of the Request & Response headers, example of how to search on a server and the expected response body as an example</td>
</tr>
</table>

## 3. Resource API's ##

This section looks at the common Spine FHIR Resources covered within this implementation guide. Most FHIR resources are specific to the API domain, so will be described in the relevant API specification, but the below are common across all Spine Core APIs:

<table style="min-width:100%;width:100%">
<tr id="conformance">
<th style="width:33%;">Foundation</th>
<th style="width:33%;"></th>
<th style="width:33%;"></th>
</tr>
<tr id="conformanced">
<th>Conformance</th>
<th>Other</th>
<th>&nbsp;</th>
</tr>
<tr>
<td><a href="api_foundation_capabilitystatement.html">Capability Statement</a></td>
<td><a href="api_spine-operationoutcome.html">Spine-OperationOutcome</a></td>
<td>&nbsp;</td>
</tr>
</table>
