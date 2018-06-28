---
title: SAML Example
keywords: legacy, security
tags: [legacy]
sidebar: foundations_sidebar
permalink: saml_example.html
summary: "An example of a SAML assertion"
---

{% include important.html content="The APIs described in this section are being replaced by [newer NHS Identity APIs](https://developer.nhs.uk/apis/national-authentication/), so implementers should be aware that these APIs will be replaced in future." %}

See the [Authorisation](legacy_authorisation.html) page for details.

Example SAML Role Assertion Request (using token ID returned from the identity agent and URL encoded):

```bash
curl --insecure https://sbapi.nis1.national.ncrs.nhs.uk/saml/RoleAssertion?token=AQIC5wM2LY4Sfcy73z2zeTRzPNXOdr5U6mC2NyUVxy0BffY%3D%40AAJTSQACMDE%3D%23
```

SAML Response:

```xml
<samlp:Response xmlns:samlp="urn:oasis:names:tc:SAML:1.0:protocol" ResponseID="null" InResponseTo="null" MajorVersion="1" MinorVersion="1" IssueInstant="2018-06-28T14:30:51Z" Recipient="172.16.143.51">
    <samlp:Status>
        <samlp:StatusCode Value="samlp:Success"/>
    </samlp:Status>
    <saml:Assertion xmlns:saml="urn:oasis:names:tc:SAML:1.0:assertion" MajorVersion="1" MinorVersion="1" AssertionID="ba10307f67ace849c6fc7459fe9b6e4e2daa65c8d" Issuer="http://iam-careid-development:8080" IssueInstant="2018-06-28T14:30:51Z">
        <saml:Conditions NotBefore="2018-06-28T14:27:51Z" NotOnOrAfter="2018-06-28T14:31:51Z"/>
        <saml:AttributeStatement>
            <saml:Subject>
                <saml:NameIdentifier NameQualifier="o=nhs">uid=232871648514,ou=People,o=nhs</saml:NameIdentifier>
                <saml:SubjectConfirmation>
                    <saml:ConfirmationMethod>urn:com:sun:identity</saml:ConfirmationMethod>
                    <saml:SubjectConfirmationData>
                        <saml:Assertion MajorVersion="1" MinorVersion="1" AssertionID="10a1792f539d58d6d11f9a84633338c27907283er" Issuer="http://iam-careid-development:8080" IssueInstant="2018-06-28T14:30:51Z">
                            <saml:Conditions NotBefore="2018-06-28T14:27:51Z" NotOnOrAfter="2018-06-28T14:31:51Z"/>
                            <saml:AuthenticationStatement AuthenticationInstant="2018-06-28T14:22:24Z" AuthenticationMethod="urn:com:sun:identity:ExtendedLoginModule">
                                <saml:Subject>
                                    <saml:NameIdentifier NameQualifier="o=nhs">uid=232871648514,ou=People,o=nhs</saml:NameIdentifier>
                                    <saml:SubjectConfirmation>
                                        <saml:ConfirmationMethod>urn:com:sun:identity</saml:ConfirmationMethod>
                                        <saml:SubjectConfirmationData>AQIC5wM2LY4Sfcy73z2zeTRzPNXOdr5U6mC2NyUVxy0BffY=@AAJTSQACMDE=#</saml:SubjectConfirmationData>
                                    </saml:SubjectConfirmation>
                                </saml:Subject>
                                <saml:SubjectLocality IPAddress="172.16.143.51"/>
                            </saml:AuthenticationStatement>
                        </saml:Assertion>
                    </saml:SubjectConfirmationData>
                </saml:SubjectConfirmation>
            </saml:Subject>
            <saml:Attribute AttributeName="ssbAssertionVersion" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>1.1</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="cn" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>Hatherly Adam</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="uid" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>232871648514</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="ssbSessionRoleUid" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>780609380511</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="ssbMode" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>0</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="nhsIdCode" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>X09</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="o" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>NHS CONNECTING FOR HEALTH</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="nhsBusinessFunctions" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>Execute Management Reports</saml:AttributeValue>
                <saml:AttributeValue>View non-ETP Clinical Data within CSA</saml:AttributeValue>
                <saml:AttributeValue>Execute Clinical Reports</saml:AttributeValue>
                <saml:AttributeValue>Claim Subject Access Request</saml:AttributeValue>
                <saml:AttributeValue>Manage Detailed Health Records</saml:AttributeValue>
                <saml:AttributeValue>Claim a relationship with a patient</saml:AttributeValue>
                <saml:AttributeValue>Execute Administrative Reports</saml:AttributeValue>
                <saml:AttributeValue>Access SCR Application (Perform Patient Trace)</saml:AttributeValue>
                <saml:AttributeValue>Execute Ad-hoc Reports</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="nhsBusinessFunctionsCodes" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>B8029</saml:AttributeValue>
                <saml:AttributeValue>B0945</saml:AttributeValue>
                <saml:AttributeValue>B0257</saml:AttributeValue>
                <saml:AttributeValue>B0264</saml:AttributeValue>
                <saml:AttributeValue>B0286</saml:AttributeValue>
                <saml:AttributeValue>B0991</saml:AttributeValue>
                <saml:AttributeValue>B8004</saml:AttributeValue>
                <saml:AttributeValue>B8006</saml:AttributeValue>
                <saml:AttributeValue>B0085</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="nhsJobRole" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>"Clinical":"Clinical Provision":"Health Professional Access Role"</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="uniqueIdentifier" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>780609380511</saml:AttributeValue>
            </saml:Attribute>
            <saml:Attribute AttributeName="nhsJobRoleCode" AttributeNamespace="http://www.syntegra.com">
                <saml:AttributeValue>S8000:G8000:R8003</saml:AttributeValue>
            </saml:Attribute>
        </saml:AttributeStatement>
    </saml:Assertion>
</samlp:Response>
```
