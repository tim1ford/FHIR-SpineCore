---
title: Gazetteer - Address Finder
keywords: legacy, messaging
tags: [legacy]
sidebar: overview_sidebar
permalink: legacy_gazetteer.html
summary: "Details of the Gazetteer/Address Finder service"
---

## Overview ##

The Gazetteer Service, also known as the Address Finder service supports the validation or retrieval of UK-based addresses. The service will fulfil requests coming from systems external to Spine as well as those from internal clients. It is an address validation service accessing a separate address list Postal Address File (PAF)  
The service requires a relatively low level of reliability and as such will implement an MHS relationship with the client in web service mode.  The service will be provided via a SOAP web service defined by a WSDL.
The Gazetteer provides the following functionality to external systems:

- Validate and transform the address to PAF standard
- Search and return address list based on search criteria 

## Gazetteer Functions ##

The Gazetteer service provides various functions for validating an address based on complete/partial address lines provided by the caller subsystems. This includes:

1. Single search result based on the house number/name and postcode.
2. Multiple search results on partial address inputs or wild card searches.
3. Fuzzy searches based on phonetics of words entered in address lines.
4. Intelligent postcode parsing to validate the underlying structure.
5. Return of the PAF address key for each delivery point.

Full details can be found in EIS 12.3 issue 11.9

### GetAddressList ###

The validation or retrieval of addresses is performed using MHS Web Service Mode in response to the request sent by the external systems with a pre-defined set of search arguments.  The response is returned as exact or multiple matches depending upon the completeness of search arguments.  The matched addresses are returned to the client subsystem as retrieved from the PAF.  The Gazetteer Service will return the lower of the total number of matched addresses found and the number of addresses requested by the client (up to the maximum number permitted by the service).  The format of each returned address line in terms of size and capitalisation feature will be configured with the Gazetteer service.  The PAF address key will also be returned along with each matched address.  The address validation process will be carried out only for the UK addresses submission of a non-UK address will result with an error message containing ‘Unable to process non UK Address’ being sent back to the client. 

The Gazetteer service will return all matched address as retrieved from the PAF file up to the service’s configured default figure where the client fails to provide a value for 'maximum number of address to be returned'.  The following table describes the possible search patterns and the corresponding response type.

#### Search Argument ####

The source systems will send up to maximum of six search arguments while calling the ‘Get Valid Address’. The search arguments normally map to typical postal address and will be as follows:

|-----|------------------------------------------| -------|--------------------|-------------------|
| No  | Argument                                 | Type   | Value              | DEFAULT           |
|-----|------------------------------------------| -------|--------------------|-------------------|
| 1.  | Address Line 1                           | CHAR   |                    |                   |
| 2.  | Address Line 2                           | CHAR   |                    |                   |
| 3.  | Address Line 3                           | CHAR   |                    |                   |	
| 4.  | Town                                     | CHAR   |                    |                   |	
| 5.  | County                                   | CHAR   |                    |                   |	
| 6.  | Post Code                                | CHAR   |                    |                   |
| 7.  | Country                                  | CHAR   | GBR=Great Britain. | GBR=Great Britain |
| 8.  | Maximum number addresses to be returned. | NUMBER | Maximum=99         | 9                 |

#### Search Capability ####


| Sl. | Search pattern | Response Type |
|1.   |Premises number or name and post code | A single match address to be returned if search arguments match a record in the address data file, otherwise an error showing the non-availability/recoded status of the address. |
|2. |Complete address excluding postcode | A single match address to be returned if search arguments match a record in the address data file, otherwise multiple addresses if the address is ambiguous. |
|3. | Fuzzy search: The search argument is passed with misspell words.<br/>e.g. 115 Roglyn Sreet in place of 115 Rosslyn Street | Single/multiple matched address depending upon the completeness of all address elements. |
|4. | (?) Wildcard search: The postcode can have a single character replacement search i.e. L?2 7DN | Single/multiple matched address depending upon the number of valid characters that can be substituted up to the maximum number of addresses requested. |
|5. | (*) Wildcard search: All address elements except the postcode will support * wildcard search representing multiple characters. | Single/multiple matched address depending upon the number of valid characters that can be substituted up to the maximum number of addresses requested. |
|6. | Postcode parsing: Intelligent parsing of postcode to detect wrong characters to possible extent as supported by Pro. | Single/Multiple address depending upon the completeness of all search arguments up to the maximum number of addresses requested. |

#### Search Mode ####

The search mode decides the search method to be followed by Gazetteer service taking the minimum number of search arguments. The following search mode will be supported by the Gazetteer service depending upon the availability of minimum address lines.

 - OPTIMAL: The premises number or name and postcode must be present as part of the search criteria.
 - ALTERNATIVE: If the Optimal mode is not possible i.e. the caller subsystem is unable to provide the premises number or name and postcode. 

#### Returned Response ####

Upon a successful search based on the argument list provided by the calling system, one or more addresses may be returned. The Get Valid Address will return multiple addresses in the desired message format as described in the following. The response from Gazetteer service will include any error message for the requesting system to report where a failure of the search process occurs, including system failures.  Each returned address will have the following format in the order shown in reference numbers 1-8 below; followed by a single integer element containing the total number of addresses matched (as this may be different to the number returned).

|--------|---------------------|-------------|---------|------------------------------------------|
| Ref No | Address Line        | Width       | Format  | Mandatory                                |
|--------|---------------------|-------------|---------|------------------------------------------|
| 1.     | Address1            | String (60) |         | Either Address1 or Address2 is required. |
| 2.     | Address2            | String (60) |         | Either Address1 or Address2 is required. |
| 3.     | Address3            | String (60) |         | No                                       |
| 4.     | Town                | String (60) | CAPITAL | Yes                                      |
| 5.     | County              | String (60) |         | No                                       |
| 6.     | Post Code           | String (8)  | CAPITAL | Yes                                      |
| 7.     | Country             | String (60) | CAPITAL | Yes                                      |
| 8.     | PAF Key             | Int         |         | Yes                                      |
| 9.     | MatchedAddressCount | Int         |         | Yes                                      |


## Interface Technical Description ##

GetAddressList: The message exchange protocol is a straight forward request-response interaction as shown in the following sequence diagram.

![Figure 4 1.  Get Address List Realisation.](images/legacy/Gazetteer.gif)
Figure 4 1.  Get Address List Realisation.

## Web Service Header ##

Gazetteer currently implements and will continue to support P1R1 version of the Web Service mode.  As defined below under Web Services mode
The Gazetteer header elements are included within the payload document in the SOAP body.
Gazetteer does not use any aspects of the nasp:securityPackage. However, this element MUST be included as an empty element.

## Error Handling Structure ##

Gazetteer returns application errors contained within the payload document in the SOAP body.

A Code Context element is included in the element schema classifying errors elements by service. This allows errors to use application specific or service specific error codes while avoiding duplication. The errorList/error/codeContext attribute for all Gazetteer errors MUST be "urn:nhs:names:errors:gazetteer".

The following table describes the possible errors returned from Gazetteer. Successful responses will contain only the resulting data set and no error element.

|-----|-------------|------------------------------|---------------------------------------|
| Sl  | Type        | Error Code (error/errorCode) | GS Reported Error (error/description) |
|-----|-------------|------------------------------|---------------------------------------|
| 1.  | System      | GSS001 | Service is not available |
| 2.  | System      | GSS002 | Invalid Address Data File. |
| 3.  | System      | GSS003 | Service startup error. |
| 4.  | System      | GSS004 | Failed to get reference of service locator. |
| 5.  | System      | GSS005 | Failed to lookup home of remote service. |
| 6.  | System      | GSS006 | Failed to get the remote service reference. |
| 7.  | System      | GSS007 | Failed to invoke address validation method. |
| 8.  | System      | GSS008 | Request time out occurred. |
| 9.  | System      | GSS009 | Failed to get the reference of remote service. |
| 10. | Business    | GSB001 | Search time out. |
| 11. | Business    | GSB002 | Too Many Matches found. |
| 12. | Business    | GSB003 | Null values found in mandatory elements. |
| 13. | Business    | GSB004 | Minimum number of address requested should at least be 1. |
| 14. | Business    | GSB005 | Unable to validate non-UK address. |
| 15. | Business    | GSB006 | Null value in GUID. |
| 16. | Application | GSA001 | Empty Search request found. |
| 17. | Application | GSA002 | Invalid Message Structure. |
| 18. | Application | GSA003 | Response Message Creation Failure. |
| 19. | Application | GSA000 | Unknown Error |

## Functional/Business Schema ## 
The Gazetteer payload document structure is made up of the following elements (all from the national.carerecords.nhs.uk/Schema/ namespace):

- Request (getAddressListRequest):
  - The messageHeader element.
  - The postalAddress element.  Contains the search parameters.
  - The maxNo element from the same namespace.
- Response
  - The messageHeader element.
  - The addressList element.  Contains a collection of postalAddress elements containing the returned matches.
  - The totalmatches element.

## Message Sample: SOAP / HTTP ##

The following shows an example of a Gazetteer getAddressListRequest.

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
	<SOAP-ENV:Header/>
	<SOAP-ENV:Body>
		<nasp:getAddressListRequest xmlns:nasp ="http://national.carerecords.nhs.uk/schema/">
			<nasp:messageHeader>
				<nasp:messageId>6e6bf479f2775defa378beea704051a4</nasp:messageId>
				<nasp:securityPackage/>
			</nasp:messageHeader>
			<nasp:postalAddress>
				<nasp:address1>1 Sandringham Crescent</nasp:address1>
				<nasp:address2/>
				<nasp:address3/>
				<nasp:town>Leeds</nasp:town>
				<nasp:county/>
				<nasp:postcode>LS178DB</nasp:postcode>
				<nasp:country/>
			</nasp:postalAddress>
			<nasp:maxno>2</nasp:maxno>
		</nasp:getAddressListRequest>
	</SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

The response may look as follows:

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
	<SOAP-ENV:Header/>
	<SOAP-ENV:Body>
		<nasp:getAddressListResponse xmlns:nasp="http://national.carerecords.nhs.uk/schema/">
			<nasp:messageHeader>
				<nasp:messageId>1f52460:fb647de31b:-7ce2</nasp:messageId>
				<nasp:securityPackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" xsi:schemaLocation="http://national.carerecords.nhs.uk/schema http://gazetteer.co.uk/GSWS/gazetteer001.xsd"/>
			</nasp:messageHeader>
			<nasp:addressList>
				<nasp:postalAddress>
					<nasp:address1>1 Sandringham Crescent</nasp:address1>
					<nasp:address2/>
					<nasp:address3/>
					<nasp:town>LEEDS</nasp:town>
					<nasp:county>West Yorkshire</nasp:county>
					<nasp:postCode>LS178DB</nasp:postCode>
					<nasp:country>UNITED KINGDOM</nasp:country>
					<nasp:addressKey>17671560</nasp:addressKey>
				</nasp:postalAddress>
			</nasp:addressList>
			<nasp:totalmatches>1</nasp:totalmatches>
		</nasp:getAddressListResponse>
	</SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## Web Service Header ##

The P1R1 approach for Non-HL7 Web Service Header used NHS CFH (now known as NHS Digital) specific elements present in the headers. The Web Service Headers are derived from the WS-Addressing specification. A full definition of Web Service mode interactions can be found in part 2 MHS section 2.6  of the EIS documentation set.

### Security Package ###

The SecurityPackage is depreciated but retained for future use.

### Non-HL7 Web Service Header ###

The message header is used in web service mode when exchanging non-HL7 messages.  It is only used for P1R1 web services, and will be depreciated in later releases. 

|----------|------|-------------|----------|
| Location | Type | Cardinality | Comments |
|----------|------|-------------|----------|
| ./messageHeader/messageId | String | 1 | A unique message identifier generated by the sender.  This maybe a standard DCE UUID or the modified form of the DCE UUID defined in Appendix A 2.6 Unique Identifiers. |
| ./messageHeader/securityPackage | Complex | 1 | The Web Service Headers are derived from the WS-Addressing specification. A full definition of Web Service mode interactions can be found in part 2 MHS section 2.6  of the EIS documentation set.
| ./messageHeader/errorList | Complex | 0..* | The structure defined in  Non-HL7 Web Services Error List |

### Non-HL7 Web Services Error List ###

The errorList element is used in web service mode when exchanging non-HL7 messages.  P1R1 web services use this element as part of a custom header.

|------------------------------|---------|-------------|----------|
| Location                     | Type    | Cardinality | Comments |
|------------------------------|---------|-------------|----------|
| errorList/error              | Complex | 0..*        | Contains a collection of error elements, annotating separate error conditions. |
| errorList/error/codeContext  | String  | 1           | This is the “namespace” of the error being provided, and will be provided for each non-HL7 web service that uses the errorList element. |
| errorList/error/errorCode    | String  | 1           | Error code as per the specification of the service. |
| errorList/error/severity     | String  | 1           | Either “Warning” or “Error”. |
| errorList/error/location     | String  | 0..1        | An xPointer that indicates which part of the message the error element relates to. |
| errorList/error/description  | String  | 1           | A description of the error. |

### Example Error Message ###

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"><SOAP-ENV:Header/>
<SOAP-ENV:Body>
<nasp:getAddressListResponse xmlns:nasp="http://national.carerecords.nhs.uk/schema/">
    <nasp:messageHeader>
        <nasp:messageId>{{messageId}}</nasp:messageId>
        <nasp:errorList>
            <nasp:error>
                <nasp:codeContext>urn:nhs:names:errors:gazetteer</nasp:codeContext>
                <nasp:errorCode> GSS008</nasp:errorCode>
                <nasp:severity>Warning</nasp:severity>
                <nasp:description> Request time out occurred</nasp:description>
            </nasp:error>
        </nasp:errorList>
        <nasp:securityPackage/>
    </nasp:messageHeader>
</nasp:getAddressListResponse>
</SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

