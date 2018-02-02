---
title: Environments
keywords: test, environments
tags: [test]
sidebar: overview_sidebar
permalink: test_environments.html
summary: "The Environments page covers the various Spine environments available for test and production use"
---

# What Spine environments are available? #

In addition to the live environment, Spine also provides a number of "path to live" environments:

- Development
- Integration
- Deployment
- Training
- Non-Functional Testing
- Opentest

The full details of these environments, including the endpoint URLs for all the services in each environment can be found in the "authority service names" documents for each environment. These documents can be downloaded from the [Assurance Support Website](http://www.assurancesupport.digital.nhs.uk/) (note: an N3 connection required to access this site).

The above site also holds copies of the parent Root and SubCA certificates which you'll need to verify the SSL certificates registered against for Spine services (all endpoint certificates are children of these parent certificates).

# What is Opentest? #

Opentest is NHS Digital's open-access network for developing and testing healthcare applications. The Opentest network consists of a hosted component, and a VPN through which users connect. The network is not N3/HSCN connected and so is accessible to users without access to those networks.

More details are avaliable [here](https://digital.nhs.uk/spine/opentest)

