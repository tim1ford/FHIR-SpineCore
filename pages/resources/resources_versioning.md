---
title: Versioning of Resources
keywords: resources, versioning
tags: [rest, fhir, versioning]
sidebar: overview_sidebar
permalink: resources_versioning.html
summary: How resources are versioned to allow local caching, and to avoid contention when updating resources
---

## Resource Versioning ##

To facilitate the management of [resource contention](http://hl7.org/fhir/http.html#concurrency), and to allow resources to be cached locally, all resources returned will include a version ID and a last updated date.

The version ID and last updated date are specified in the "meta" section of the resource - for example:

```json
<Flag xmlns="http://hl7.org/fhir">
	<id value="744eec7d-8951-4722-ad74-dc34e86d4e1a"/>
	<meta>
		<versionId value="25777f7d-27bc"/>
		<lastUpdated value="2013-02-02T12:02:47+00:00"/>
		<profile value="https://fhir.nhs.uk/STU3/StructureDefinition/RARecord-Flag-1"/>
	</meta>
	<!-- Flag resource content -->
</Flag>
```

The version ID value is specific to the version of the flag (in the above example: **25777f7d-27bc**), and will change every time this specific flag resource is updated (the resource ID in the "id" field will remain the same). The version ID can be any value valid as per the [id datatype](https://www.hl7.org/fhir/datatypes.html#id), and may not be a sequential number - clients should not infer any specific meaning on the values of the ID. The lastUpdated date is the date when the resource was updated to the specified version. 

Resources returned in a bundle will each have their own ID and versionID values within the resource, but in addition, where a client does a READ on an individual resource by ID, the versionID and last updated date for the resource will also be returned in an ETag and Last-Modified HTTP headers:

```json
HTTP 200 OK
Date: Sat, 09 Feb 2013 16:09:50 GMT
Last-Modified: Sat, 02 Feb 2013 12:02:47 GMT
ETag: W/"25777f7d-27bc"
Content-type: application/json+fhir

... Resource content as above ...
```

ETag headers which denote resource version IDs SHALL be prefixed with W/ and enclosed in quotes, for example:

```json
ETag: W/"25777f7d-27bc"
```

NOTE: The above does not mean that the API provides access to older versions of resources - the versionID for older versions of a resource are not made available (for example through a VREAD operation).

