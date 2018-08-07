---
title: Updating Resources
keywords: resources, update
tags: [rest, fhir, update]
sidebar: overview_sidebar
permalink: update.html
summary: How resources are updated on Spine endpoints using the ReST API
---

## Update Interaction ##

Not all APIs on the Spine support updating resources, so you should refer to the specific API definition, or the CapabilityStatement in the Spine endpoint to understand whether resource create operations are supported for specific resources.

As per the [FHIR spec](https://www.hl7.org/fhir/http.html#update), the update operation is a HTTP PUT request, with the ID of the resource to be updated in the URL:

**Request Example**

```json
PUT https://clinicals.spineservices.nhs.uk/STU3/[resourceType]/[id] /HTTP1.1
If-Match: W/"[versionID]"

{
  "resourceType": "[resourceType]",
  "id": "[id]",
  "meta": {
    "profile": [
      "[profile]"
    ]
  },
  ... RESOURCE CONTENT ...
}
```

- The resource submitted in the request body SHOULD NOT contain versionID or lastUpdated fields in the meta section as these are added by the Spine. If any are provided they will be overwritten when the resource is created.
- The ID used in the PUT URL is the unique ID of the resource on Spine - this will have been returned from a previous create or search interaction.
- The versionID in the If-Match header is used to manage update conflicts (see below for details).

**Response Example**

If the update is successful, the response will be a HTTP 200 OK response:

```json
HTTP 200 OK
Date: Fri, 25 May 2018 16:09:50 GMT
Last-Modified: Fri, 25 May 2018 16:09:50 GMT
ETag: W/"25777f7d-27bc"
Location: https://clinicals.spineservices.nhs.uk/STU3/Subscription/ea0a4851-8720-4b49-b978-bdcf7102388c
```

## Managing Conflicting Updates ##

There is a risk in some cases that two clients will try to update the same resource. If both clients had the same version of that resource to begin with, there is a risk that the second overwrites the updates of the first. To prevent this happening, clients MUST submit update requests with an If-Match header that quotes the ETag from the server (see the [versioning page](explore_versioning.html) for details of version IDs). This specifies the version of the resource that their updates should be applied to, so a second attempt to update the same version with different changes can be detected as a conflict and rejected (this is also known as optimistic locking).

Updates would include the versionID in the HTTP header as follows:

```
PUT https://clinicals.spineservices.nhs.uk/STU3/List/744eec7d-8951-4722-ad74-dc34e86d4e1a
If-Match: W/"25777f7d-27bc"
```

If the version Id given in the If-Match header does not match, the server returns a 409 Conflict status code instead of updating the resource.

**IMPORTANT NOTE**: All updates to resources MUST include an If-Match header or they will be rejected. The server SHALL return a 412 Precondition Failed in this case.

## Response Preference ##

Any errors when updating resources will always result in an OperationOutcome containing details of the error (see [Error Handling](http://localhost:4005/resources_error_handling.html))

In addition to the default response behaviour above, clients can express a preference for alternate response behaviours using a **Prefer** header in the same way as described for the [create interaction](create.html#response-preference)
