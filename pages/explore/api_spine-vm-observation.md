---
title: Clinical | API Spine VM Observation
keywords: getcarerecord, structured, rest, condition
tags: [rest,fhir,condition,clinical,api]
sidebar: accessrecord_rest_sidebar
permalink: api_spine-vm-observation.html
summary: Measurements and simple assertions made about a patient, device or other subject.
---
{% include custom/search.warnbanner.html %}

{% include custom/fhir.reference.html resource="Observation" page="spine-vm-observation-1" fhirlink="[Observation](https://www.hl7.org/fhir/observation.html)" content="N/A" userlink=" " %}


## 1. Read ##

GET the average, min, max and count of a series of BP measurements for a patient (Request):
<div markdown="span" class="alert alert-success" role="alert">
GET [base]/Observation/$stats?subject=Patient/123&code=55284-4&system=http://loinc.org&duration=1&statistic=average&statistic=min&statistic=max&statistic=count</div>

## 2. Search ##

<div markdown="span" class="alert alert-success" role="alert">
GET [baseUrl]/Observation?[searchParameters]</div>

{% include custom/search.header.html resource="Observation" %}

### 2.1. Search Parameters ###

{% include custom/search.parameters.html resource="Observation"     link=" http://hl7.org/fhir/OperationDefinition/Observation-stats" %}

## 3. Example ##

#### 3.1 Http Body ####

<script src="https://gist.github.com/IOPS-DEV/914d453110ae00c185ca844e93512c18.js"></script>