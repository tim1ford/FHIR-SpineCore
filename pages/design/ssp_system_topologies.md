---
title: System Topologies
keywords: endpoint, mhs, asid
tags: [design]
sidebar: overview_sidebar
permalink: ssp_system_topologies.html
summary: "Overview of the different types of deployment topologies for brokering API calls through the SSP"
---

{% include important.html content="The topologies depicted are illustrative rather than prescriptive."%}

{% include important.html content="The Spine Secure Proxy includes a mechanism to filter out all requests between organisations that are not registered on the proxy as having a mutual Data Sharing Agreement. Without this then all consumers would be able to send requests to all API Providers. 
In order for the filtering solution to work each consumer/provider organisation needs their [Spine ASID](ssp_system_topologies.html#spine-endpoint-terms) configured on the SSP."%}

# Consumer Topologies #

## Simple Model ##
![Simple Topology](images/integration/consumer-topology1-simple.png)<br>

A grouping of different GP Connect consumer systems, all connecting directly to GP Connect via the SSP.  Each consumer in this example is registered as a CMA endpoint.  The key point is that each consumer system has its own ASID.

## Aggregator Model ##

![Simple Model](images/integration/consumer-topology2-aggregator.png)

Several different consumer systems connecting to GP Connect via middleware (Message Handling Server / MHS)

# Provider Topologies #

## Single Practice System ##

![Single Practice System](images/integration/provider-topology1-single.png) 

A discrete instance of a Primary Care System serving a single GP Practice.

## Data Centre Hosted Practice System ##

![Hosted Practice System](images/integration/provider-topology2-datacentre.png) 

A GP Practice system instance hosted in a Primary Care System (PCS) supplier's data centre.  Note each individual practice has a logical CMA endpoint with its own ASID but sharing the Party Key.

![Legend](images/integration/topologies-legend.png)

## Spine Endpoint Terms ##

The terms used above are explained in detail on the [SDS endpoint lookup page](build_endpoints.html).
