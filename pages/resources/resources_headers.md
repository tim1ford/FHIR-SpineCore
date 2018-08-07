---
title: HTTP Headers
keywords: resources, headers
tags: [rest, fhir, headers]
sidebar: overview_sidebar
permalink: resources_headers.html
summary: HTTP Headers for Spine FHIR&reg; Interactions
---

## Standard FHIR&reg; Headers ##

Spine FHIR APIs support most standard FHIR HTTP headers in their requests and responses. Individual API specifications may give examples, and there may be exceptions where a specific API does not support all these parameters, but the below is a summary of the supported headers used in Spine FHIR APIs:

### Request Headers ###

|-------------------------------|--------------------------|-------------------------------------------------------------------------------|
| HTTP Header                   | Mandatory/Optional       | Description                                                                   |
|-------------------------------|--------------------------|-------------------------------------------------------------------------------|
| ```Accept```                  | Optional                 | Allows the client to specify the content encoding they want for responses (see "Content types" below) |
| ```Content-Type```            | Optional                 | Allows the client to specify the content encoding of the item they are submitting (see "Content types" below) |
| ```Accept-Encoding```         | Optional                 | Allows the client to request compressed responses (see "Content compression" below) |
| ```Transfer-Encoding```       | Optional                 | Allows the client to request chunked responses (see "Transfer encoding" below) |
| ```If-Match```                | Mandatory for Update/Delete | Specify the version of the resource that is being updated/deleted (see [Resource Versioning](resources_versioning.html)) |
| ```If-None-Match```           | Optional                 | Allows clients to cache READs (see the [read interaction](read.html) |
| ```If-Modified-Since```       | Optional                 | Allows clients to cache READs (see the [read interaction](read.html) |
| ```Prefer```                  | Optional                 | Allows the client to specify the response behaviour for create/update operations (see the [create interaction](create.html#response-preference)) |


### Response Headers ###

|-------------------------------|-------------------------------------------------------------------------------|
| HTTP Header                   | Description                                                                   |
|-------------------------------|-------------------------------------------------------------------------------|
| ```Date```                    | The Date/Time the response was created |
| ```Content-Type```            | This is used by the Spine to indicate the content encoding of the response (see "Content types" below) |
| ```ETag```                    | This is the versionID for the resource that has been created/updated (see [Resource Versioning](resources_versioning.html)) |
| ```Last-Modified```           | The Date/Time the resource in the response was last modified (see [Resource Versioning](resources_versioning.html)) |
| ```Location```                | The URL for a newly created/updated resource (see the [create interaction](create.html)) |


## Other Headers ##

In addition to the FHIR&reg; standard headers, the Spine requires some additional HTTP headers to manage system access controls and routing of requests:

### Request Headers ###

|-------------------------------|--------------------------|-------------------------------------------------------------------------------|
| HTTP Header                   | Mandatory/Optional       | Description                                                                   |
|-------------------------------|--------------------------|-------------------------------------------------------------------------------|
| ```Authorization```           | Mandatory                | This is an OAuth bearer token with details of the user, system and organisation context (see [Access Tokens and Audit](security_jwt.html)) |
| ```FromASID```               | Mandatory                | The Accredited System ID of the client (see [Spine Endpoints](build_endpoints.html)) |
| ```ToASID```                 | Mandatory                | The Accredited System ID of server. For Spine APIs this will be the Spine API, but for calls brokered through the [SSP](ssp_overview.html) this would be the target server ASID |
| ```InteractionID```          | Mandatory                | This is the system interaction being performed (see [Interaction IDs](security_interaction_ids.html)) |

## Proxying headers ##

When proxying requests through the SSP, additional HTTP headers SHALL be added into the HTTP request/response for allowing the proxy system to disclose information lost in the proxying process (for example, the originating IP address of a request). Typically, this information is added to proxy forwarding headers as defined in [RFC 7239](https://tools.ietf.org/html/rfc7239).

## HTTP Guidance ##

### Content types ###

- The Spine supports both formal [MIME-types](https://www.hl7.org/fhir/STU3/http.html#mime-type) for FHIR resources:
  - JSON: `application/fhir+json`
  - XML: `application/fhir+xml`

- The preferred way for a client to specify the encoding they wish to use is in an **Accept** header:

```json
Accept: application/fhir+json
```

- The Spine also supports the optional `_format` parameter in order to allow the client to specify the response format by its MIME-type. If both are present, the `_format` parameter overrides the `Accept` header value in the request.

- Where no `Accept` header or `_format` parameter is sent with a request but there is a `Content-Type` header in the request (such as in a POST request), the Spine SHALL return the response using the format specified in the request `Content-Type` header.

- Where no `Accept` header, `_format` parameter and no `Content-Type` header is present, as is possible within a GET request, the Spine SHALL default to JSON.

### Transfer encoding ###

The Spine supports the HTTP [Transfer-Encoding](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) header with a value of `chunked`:

```json
Transfer-Encoding: chunked
```

This indicates that the body of a HTTP response will be returned as an unspecified number of data chunks (without an explicit `Content-Length` header).

### Character encoding ###

The Spine uses `UTF-8` [character encoding](https://www.hl7.org/fhir/STU3/http.html#mime-type) as outlined in the FHIR standard. All Spine FHIR responses will use this encoding regardless of any `Content-Type` or `Accept-Charset` headers in requests.

### Content compression ###

To improve system performances clients/servers SHALL support GZIP compression.

Compression is requested by setting the `Accept-Encoding` header to `gzip`:

```json
Accept-Encoding: gzip
```

{% include tip.html content="Applying content compression is key to reducing bandwidth needs and improving battery life for mobile devices." %} 

