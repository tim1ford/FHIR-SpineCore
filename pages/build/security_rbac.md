---
title: Role Based Access Controls
keywords: security, authentication, authorisation, rbac
tags: [design]
sidebar: overview_sidebar
permalink: security_rbac.html
summary: "Details of the national Role Based Access Control model."
---

RBAC is concerned with controlling which users can have access to which application functions, based on the roles(s) they perform, and consequently what kinds of data they can access. It is an integral part of the security process for accessing the NHS Digital Spine services. When users go through the registration process to be granted a Smart Card and then register one or more Role Profiles for use within an application they are using RBAC. 
By registering a Role Profile details of the organisation for which that individual works and the job role they fulfil can be passed to an application. This job role may be associated with certain business functions and these are also passed to the application and allow or restrict access to certain parts of the application at both a national and local level.
 
There are several hundred available attributes that may be granted in different combinations to support the complex access control requirements of the NHS. The harmonised approach to RBAC was developed to address this complexity problem so that the full set of access rights is determined by the set of activities that accrue through the national baseline policy and any additional activities allocated explicitly at a local level. The national baseline policy is the minimum set of things someone with a specific job role should be able to do in that job role. An individual’s Role Profile may also be associated with:

- One or more Work Groups
- Areas of Work
- Business Functions 

and so can build up a complex profile of what they are allowed to see and do within an application. The relationship between these is illustrated below. 

![RBAC Overview Diagram](images/RBAC-Diagram.png)

The [National RBAC Database (NRD)](https://digital.nhs.uk/services/registration-authorities-and-smartcards#section-2) contains the national Role Based Access Control (RBAC) attribute definitions for job roles, areas of work and activities along with the national Baseline Policy.

