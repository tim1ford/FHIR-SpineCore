---
title: Creating Resources
keywords: resources, create
tags: [rest, fhir, create]
sidebar: overview_sidebar
permalink: create.html
summary: How resources are created on Spine endpoints using the ReST API
---

## Create Interaction ##

Not all APIs on the Spine support creating resources, so you should refer to the specific API definition, or the CapabilityStatement in the Spine endpoint to understand whether resource create operations are supported for specific resources.

As per the [FHIR spec](https://www.hl7.org/fhir/http.html#create), the create is a simple HTTP POST request, with the resource to be created in the body of the request:

**Request Example**

```json
POST https://clinicals.spineservices.nhs.uk/STU3/[resourceType] /HTTP1.1

{
  "resourceType": "Subscription",
  "meta": {
    "profile": [
      "https://fhir.nhs.uk/STU3/StructureDefinition/EMS-Subscription-1"
    ]
  },
  ... RESOURCE CONTENT ...
}

```

NOTE: The resource in the body MUST NOT include an ID element as IDs are always assigned by Spine services.

**Response Example**

Assuming the resource has been successfully received and validated by Spine, it will assign an ID for the resource. The HTTP response will be a "201 Created" HTTP status code, and SHALL also return a **Location** header which contains the new ID of the created Subscription resource, and an **ETag** header with the versionID for the specific resource version (see the [versioning page](resources_versioning.html) for details of version IDs).

```json
HTTP 201 Created
Date: Fri, 25 May 2018 16:09:50 GMT
Last-Modified: Fri, 25 May 2018 16:09:50 GMT
ETag: W/"25777f7d-27bc"
Location: https://clinicals.spineservices.nhs.uk/STU3/Subscription/ea0a4851-8720-4b49-b978-bdcf7102388c
```

## Response Preference ##

Any errors when creating resources will always result in an OperationOutcome containing details of the error (see [Error Handling](http://localhost:4005/resources_error_handling.html))

Clients creating or updating resources may also express a preference for what is returned when the resource is successfully created/updated. They can indicate this preference using the HTTP Prefer header. There are three possible values for this header:

|---------------------------------|---------------------------------------------------------------------------------------------|
| Header Value                    | Response Behaviour                                                                          |
|---------------------------------|---------------------------------------------------------------------------------------------|
| Prefer: return=minimal          | Return just the HTTP headers and an empty body (default behaviour - as per above example)   |
| Prefer: return=representation   | Return the HTTP headers, but also include the created/updated resource in the response body |
| Prefer: return=OperationOutcome | Return an OperationOutcome containing any information about the resource creation/update    |
|---------------------------------|---------------------------------------------------------------------------------------------|

### Example of return=representation ###

```json
HTTP 201 Created
Date: Fri, 25 May 2018 16:09:50 GMT
Last-Modified: Fri, 25 May 2018 16:09:50 GMT
ETag: W/"25777f7d-27bc"
Location: https://clinicals.spineservices.nhs.uk/STU3/Subscription/ea0a4851-8720-4b49-b978-bdcf7102388c

{
  "resourceType": "Subscription",
  "id": "ea0a4851-8720-4b49-b978-bdcf7102388c",
  "meta": {
    "lastUpdated": "2018-05-25T16:09:50+00:00",
    "versionID": "25777f7d-27bc",
    "profile": [
      "https://fhir.nhs.uk/STU3/StructureDefinition/EMS-Subscription-1"
    ]
  },
  ... RESOURCE CONTENT ...
}
```

The resource in the body will be a copy of the same resource that was submitted, but with the **id**, **versionId** and **lastUpdated** values added.

### Example of return=OperationOutcome ###

```json
HTTP 201 Created
Date: Fri, 25 May 2018 16:09:50 GMT
Last-Modified: Fri, 25 May 2018 16:09:50 GMT
ETag: W/"25777f7d-27bc"
Location: https://clinicals.spineservices.nhs.uk/STU3/Subscription/ea0a4851-8720-4b49-b978-bdcf7102388c

{
  "resourceType": "OperationOutcome",
  "id": "eb9e0b95-4eea-4654-ab2d-d6ef9e86cc3a",
  "meta": {
    "profile": [
      "https://fhir.nhs.uk/STU3/StructureDefinition/Spine-OperationOutcome-1"
    ]
  },
  "issue": [
    {
      "severity": "information",
      "code": "informational",
      "details": {
        "coding": [
          {
            "system": "https://fhir.nhs.uk/STU3/CodeSystem/Spine-ErrorOrWarningCode-1",
            "code": "RESOURCE_CREATED",
            "display": "Successfully created resource with ID: ea0a4851-8720-4b49-b978-bdcf7102388c"
          }
        ]
      },
      "diagnostics": "Successfully created resource with ID: ea0a4851-8720-4b49-b978-bdcf7102388c"
    }
  ]
}
```
