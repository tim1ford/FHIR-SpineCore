---
title: Authorisation and Roles
keywords: legacy, security
tags: [legacy]
sidebar: foundations_sidebar
permalink: legacy_authorisation.html
summary: "This page provides an overview of the current approach for retrieving roles for applying RBAC controls"
---

{% include important.html content="The APIs described in this section are being replaced by [newer NHS Identity APIs](https://developer.nhs.uk/apis/national-authentication/), so implementers should be aware that these APIs will be replaced in future." %}

# Overview #

The national RBAC model is described in mroe detail [here](security_rbac.html). This page describes the current process for applying RBAC controls in systems connecting to the Spine, and for systems that want to use national RBAC controls to manage access in local health and care IT systems.

In future, it will be possible to apply national RBAC controls through a national OAuth authorisation endpoint as part of the NHS Identity platform, but currently local systems are expected to apply appropriate RBAC controls. Where systems are accessing nationally held data or services on Spine, the assurance process will be used to ensure controls have been applied in systems before access will be granted.

# SSB SAML endpoint #

The Spine exposes an API that can be used to retrieve the details of the authenticated user to support local authorisation decisions.


