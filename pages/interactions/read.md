---
title: Reading Resources
keywords: resources, read
tags: [rest, fhir, create]
sidebar: overview_sidebar
permalink: read.html
summary: How resources are retrieved from Spine endpoints using the ReST API
---

## Read Interaction ##

As per the [FHIR spec](https://www.hl7.org/fhir/http.html#read), the read operation is a simple HTTP GET request, with the ID of the resource in the URL:

**Request Example**

```json
GET https://clinicals.spineservices.nhs.uk/STU3/[resourceType]/[id] /HTTP1.1
```

The ID is the unique ID of the resource on Spine - this will have been returned from a create or search interaction.

**Response Example**

Assuming the resource exists on Spine it will be returned in the HTTP response, with a 200 response code:

```json
HTTP 200 OK
Date: Fri, 25 May 2018 16:09:50 GMT
Last-Modified: Fri, 25 May 2018 16:09:50 GMT
ETag: W/"25777f7d-27bc"

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

## Caching READs ##

When a client does a READ operation to access a specific resource by ID, it may choose to include an ```If-None-Match``` header to specify the version ID of the latest version they have of that resource (see the [versioning page](resources_versioning.html) for details of version IDs) - for example:

```json
GET https://clinicals.spineservices.nhs.uk/STU3/Flag/744eec7d-8951-4722-ad74-dc34e86d4e1a
If-None-Match: W/"25777f7d-27bc"
```

If the version ID specified in the HTTP header matches the latest held on Spine, a HTTP 304 Not Modified status code will be returned to inform the client that they already have the latest version:

```json
HTTP/1.1 304 Not Modified
Date: Mon, 18 Sep 2015 22:20:01 GMT
```

If a newer version of the resource does exist, it will be returned as normal, with the newer versionID as described above so the client can update its cache.

Similarly, a client can query for updates that have a Last-Modified data newer than the last time they queried, by specifying a date in an [If-Modified-Since](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/If-Modified-Since) HTTP header:

```json
GET https://clinicals.spineservices.nhs.uk/STU3/Flag/744eec7d-8951-4722-ad74-dc34e86d4e1a
If-Modified-Since: Wed, 21 Oct 2015 07:28:00 GMT
```


