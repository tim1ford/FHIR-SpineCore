---
title: Introduction to Spine Core FHIR API Framework
keywords: homepage
tags: [overview]
sidebar: overview_sidebar
permalink: index.html
toc: false
summary: A brief introduction to getting started with FHIR&reg; APIs for national Spine services.
---

{% include important.html content="This site is under active development by NHS Digital and is intended to provide all the technical resources you need to successfully develop the APIs. This project is being developed using an agile methodology so iterative updates to content will be added on a regular basis." %}

{% include warning.html content="This site is provided for information only and is intended for those engaged with NHS Digital on the development of the APIs. It is advised not to develop against these specifications until a formal announcement has been made." %}

# Introduction #

Traditionally, the "Spine" has provided national services through a range of messaging standards including ebXML and SOAP messages. Many of these interfaces are being migrated to make use of HL7 FHIR standards. This documentation covers the common capabilities in the Spine for managing and processing national FHIR APIs.

There are two main classes of APIs that are covered by this guidance:

- FHIR APIs for national NHS Digital services such as [Visitors and Migrants](http://developer.nhs.uk/apis/vandm), [National Opt-Out]() and the [National Record Locator Service](https://developer.nhs.uk/apis/nrls/).
- FHIR APIs in external systems brokered through the Spine Secure Proxy such as [GP Connect](https://developer.nhs.uk/apis/gpconnect/).

This specification covers the common functionality for all the above nationally defined APIs processed through the Spine.

NOTE: This specification does not cover FHIR messaging, or the transfer of FHIR "documents", for information on national standards for this, refer to the [ITK3 specifications]().
