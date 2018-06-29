---
title: Smartcard Authentication
keywords: legacy, security
tags: [legacy]
sidebar: foundations_sidebar
permalink: smartcards.html
summary: "This page provides an introduction to how the current smartcard authentication process works"
---

{% include important.html content="The APIs described in this section are being replaced by [newer NHS Identity APIs](https://developer.nhs.uk/apis/national-authentication/), so implementers should be aware that these APIs will be replaced in future." %}

# How do the current CIS smartcards work? #

The current live Authentication service, which has been migrated across from Spine 1 into Spine 2, is referred to as the Care Identity Service (CIS), and it maked use of Smartcards to provide strong authentication for health and care professionals, to control access to national services. It can also be used to control access to local services, providing a form of "single sign-on".

There is a general overview of the processes around registering new staff through registration authorities, with links to further information [here](https://digital.nhs.uk/services/registration-authorities-and-smartcards).

The below sections will define the technical interactions and APIs used for authentication in more detail.

## Overall Authentication / Authorisation Process ##

The below diagram shows the interactions between the various components used to authenticate and authorise users using the current CIS services:

![Authentication / Authorisation Process Sequence Diagram](images/legacy/SpineLegacyAuthentication.gif)

The steps shown in the diagram are detailed below.

| Step | Description |
|------|-------------|
| 1    | User inserts their smart card or attempts to access a protected resource.                     |
| 2    | The IA prompts the User for a smart card and Passcode.                                        |
| 3    | The SSB Service validates the User credentials and, if successful, establishes a Session.     |
| 4    | SSB returns details of the session to the IA, including properties such as the Unique User ID (UID), a Token ID and other Session attributes, e.g. max_idle_time |
| 4b   | The roles associated with the user are also returned to the IA, and if there are multiple roles the IA will prompt the user to select the role they are acting in for the duration of the login session |
| 5    | User starts an application, which obtains the Token ID from the IA (See **Ticket API** below). |
| 6    | The healthcare application can use the Token to validate that the SSO session is valid and is still active on the Spine (See **Access Manager API** below). |
| 7    | The healthcare application can then call the SSB SAML endpoint to retrieve attributes about the user and role (see [Authorisation and Roles](legacy_authorisation))

## Identity Agent ##

The latest version (and all previous versions) of the Identity Agent can be downloded from [here](http://nww.hscic.gov.uk/dir/downloads/) (N3 connection required).

The identity agent, once installed on a client PC, exposes two APIs for client software to use to interact with the identity agent - the Ticket API, and the PKCS#11 API, each of which is described below.

In addition, the Identity Agent traps specific card events including:

 - **Card Insert Event**: This is triggered by either physically inserting a smartcard into a reader, or presenting a proximity smartcard to the reader. Once triggered, the user is prompted to enter their pin to establish a session on the Spine (see steps 1-5 above)
 - **Card Removal Event**: This is triggered by removing the smartcard from the reader, and automatically triggers a call to the SSB to destroy the token and invalidate the login session.

The Identity Agent deals with all the Authentication interactions between the smartcard reader and the Spine, and makes use of specific Gemplus drivers and protocols, linked to the Smartcard technologies used. This means that Spine authentication is currently dependent on the Identity Agent, and is therefore limited to platform on which the IA can be installed. Some work has been done on implementing third party Identity Agents to overcome this limitation, but no support can be given by NHS Digital for such third party agents.

The new [NHS Identity platform](https://developer.nhs.uk/apis/national-authentication/) will be implementing a new standards-based Authentication endpoint, which will use OpenID Connect standards. These are commonly used in many platforms and libraries, so should make authentication across a wide range of platforms and technologies much easier. Once this has been fully rolled out and adopted in client systems, the Identity Agent will no longer be required, and the APIs on this page will no longer need to be used. The new NHS Identity service will however continue to support these APIs for backwards-compatibility for some time to come.

### Ticket API ###

This is an API exposed on the Identity Agent on the user's machine. Code libraries are distributed with the identity agent to allow the API to be accessed from C or Java code on the client.

Locally installed clients on a user's machine can access this API directly to obtain a ticket. Web applications wishing to make use of this API, browser plugins are provided to allow this to be done within the user's browser and passed into the healthcare system.

The APIs provide the following methods that can be called by clients:
- **getTicket()**: Returns the ticket if available or otherwise requests one (by prompting the user to insert their smartcard and enter their pin)
- **getTicketNoAuth()**: If a ticket is available (from an existing login session), this is returned. Otherwise no value is returned. This method will not prompt the user to authenticate themselves if a session is not already active.

The below sample code shows how to call the API from a Java application:

```
import com.gemplus.gemauth.api.GATicket;

public class AuthenticateExample {
	public void authenticate() throws Exception {
		GATicket gat = new GATicket();
		String ticket = gat.getTicket();
		Log.info("Ticket returned by identity agent: '" + ticket + "'");
	}
}
```

### PKCS#11 API ###

This is an API exposed on the Identity Agent on the user's machine. Code libraries are distributed with the identity agent to allow the API to be accessed from C or Java code on the client.

The [PKCS#11 API](https://en.wikipedia.org/wiki/PKCS_11) (also known as Cryptoki) was originally developed by RSA and provides applications with an interface for a wide variety of cryptographic capabilities using hardware security modules such as, but not limited to, smart cards. A common use-case for this API is for the purposes of the Content Commitment. The API  provides the ability to digitally sign information based on an asymmetric private key and associated digital certificate stored on the user's smart card. This functionality is currently used in systems dealing with electronic prescriptions to allow the prescriptions to be digitally signed using the user's smartcard.

## Access Manager API ##

This is an API exposed by the SSB on the Spine.

{% include note.html content="This page will be updated with details in a future version of this specification." %}

