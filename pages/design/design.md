---
title: Overview | Design & Build 
keywords: design, build, access, security, overview
tags: [design, overview]
sidebar: foundations_sidebar
permalink: design.html
summary: "Describes the steps required to design & build an API using the profiles described in Explore"
---

{% include important.html content="All information provided below is indicative and subject to on-going review." %}

This section covers some of the pre-requisites for most API calls on Spine.

Broadly, in most cases the overall steps required when making calls into Spine APIs are (please see individual API specs, and the relevant sections below for more details):

- Before making the API call:
  - Develop your client as per the relevant API specification for the service you are wanting to integrate with
  - Test against a test endpoint, and engage with the NHS Digital assurance team to support you through the testing and [assurance process](assure.html).
    - Some services have internet accessible reference implementations, others can be accessed through [OpenTest](test_environments.html).
  - Go through the formal assurance process to gain access to a [path-to-live](test_environments.html), and subsequently a live environment for a deployment of your application into a health or social care service.
  - The output of the above will be the creation of an Accredited System ID for your deployment, and a client certificate for you to use to secure your API calls.

- When making an API call:
  - Do the relevant lookups on the [Spine Directory Service](build_directory.html) to establish the relevant [endpoint information](build_endpoints.html) for the API call
  - In APIs calls relating to a patient, first ensure you have traced the patient's NHS number on [PDS](pds_overview.html)
  - In future:
    - Request authentication and authorisation from [NHS Identity](https://developer.nhs.uk/apis/national-authentication/) for the data you want to read/update
    - Use the [Access token](security_jwt.html) returned from this service in your API calls
  - Currently:
    - Authenticate the user using an NHS [smartcard](smartcards.html)
    - Do local authorisation checks in your system based on the user's national roles in their [SAML token](legacy_authorisation.html)
    - Construct an [Access token](security_jwt.html) to carry this user context in your API calls
  - Make the API call using the URL from SDS and the appropriate Acccess token


