---
title: Foundation | Capability Statement
keywords: foundations, fhir
tags: [foundation,use_case,fhir,rest,api,noccprofile]
sidebar: accessrecord_rest_sidebar
permalink: api_foundation_capabilitystatement.html
summary: A value set specifies a set of codes drawn from one or more code systems.
---

{% include custom/fhir.referencemin.html resource="[Capability Statement](https://fhir.nhs.uk/STU3/StructureDefinition/NHSDigital-CapabilityStatement-1)" page="" fhirlink="[Capability Statement](https://www.hl7.org/fhir/capabilitystatement.html)" content="User Stories" userlink="" %}


## 1. Read ##

<div markdown="span" class="alert alert-success" role="alert">
GET [baseUrl]/metadata</div>

The /metadata path on the root of the FHIR server will return the Capability statement for the FHIR server:

Alternatively, a HTTP OPTIONS request against the root of the FHIR server will also return the capability profile:

<div markdown="span" class="alert alert-success" role="alert">
OPTIONS [baseUrl]/</div>

NOTE: There are a number of Spine FHIR endpoint URLs, some of which may service more than one API domain. For example, the main Spine Clinicals FHIR endoint is likely to service requests for [Reasonable Adjustments](https://developer.nhs.uk/apis/reasonable-adjustments/) as well as for [NRLS](https://developer.nhs.uk/apis/nrls/). In these cases, the CapabilityStatement resource for the endpoint will include the capabilities of both these API domains to give the client the full set of resources and operations supported.

## 2. Example ##

### 2.1 Request Query ###

Retrieve the Capability statement from the FHIR Server, the format of the response body will be xml. Replace 'baseUrl' with the actual base Url of the FHIR Server.

#### 2.1.1. cURL ####

{% include custom/embedcurl.html title="Get CapabilityStatement" command="curl -X GET -H 'Accept: application/xml+fhir' -H 'Authorization: Bearer [token]' -v 'https://clinicals.spineservices.nhs.uk/STU3/metadata'" %}

### 2.2 Response Body ###

{% include important.html content="The following capability statement is an example only" %}

<div class="language-http highlighter-rouge">
<pre class="highlight">
<h4>TO BE COMPLETED</h4>
</pre>
</div>


### 2.3 C# ###

{% include tip.html content="C# code snippets utilise Ewout Kramer's [fhir-net-api](https://github.com/ewoutkramer/fhir-net-api) library which is the official .NET API for HL7&reg; FHIR&reg;." %}

```csharp
var client = new FhirClient("http://[fhir_base]/");
client.PreferredFormat = ResourceFormat.Json;
var resource = client.CapabilityStatement();
FhirSerializer.SerializeResourceToXml(resource).Dump();
```
