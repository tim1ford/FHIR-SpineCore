---
title: Deleting Resources
keywords: resources, delete
tags: [rest, fhir, delete]
sidebar: overview_sidebar
permalink: delete.html
summary: How resources are deleted on Spine endpoints using the ReST API
---

## Delete Interaction ##

Not all APIs on the Spine support deleting resources, so you should refer to the specific API definition, or the CapabilityStatement in the Spine endpoint to understand whether resource create operations are supported for specific resources.

As per the [FHIR spec](https://www.hl7.org/fhir/http.html#delete), the delete operation is a HTTP DELETE request, with the ID of the resource to be deleted in the URL:

**Request Example**

```json
DELETE https://clinicals.spineservices.nhs.uk/STU3/[resourceType]/[id] /HTTP1.1
If-Match: W/"[versionID]"
```

- The ID used in the DELETE URL is the unique ID of the resource on Spine - this will have been returned from a previous create or search interaction.
- The versionID in the If-Match header is used to ensure the client had the latest version of the resource to begin with, and is not attempting to delete an old version of the resource. If the versionID provided does not match the one held on Spine, the delete will be rejected with a HTTP 409 Conflict status code.

**Response Example**

If the delete is successful, the response will be a HTTP 204 No Content response, indicating that the delete was successful and that there is no response body.

```json
HTTP 204 No Content
Date: Fri, 25 May 2018 16:09:50 GMT
```
