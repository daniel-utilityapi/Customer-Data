# CDSC-WG1-02 - Client Registration

## Abstract <a id="abstract" href="#abstract" class="permalink">🔗</a>

This specification defines how utilities and other central entities ("Servers") may allow third parties ("Clients") to register, configure, and maintain connectivity with the utility or other central entity. This specification mostly extends various OAuth specifications ([Dynamic Client Registration](https://www.rfc-editor.org/rfc/rfc7591), [Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414), and more) to create a robust framework for offering secure, streamlined, and standardized connectivity between utilities and third parties.

## Status <a id="status" href="#status" class="permalink">🔗</a>

<b style="color:red">WARNING: This specification is a DRAFT and has not achieved consensus by the working group.</b>

## Copyright <a id="copyright" href="#copyright" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## Table of Contents <a id="table-of-contents" href="#table-of-contents" class="permalink">🔗</a>

* [1. Introduction](#introduction)  
* [2. Terminology](#terminology)  
* [3. Authorization Server Metadata](#auth-server-metadata)  
    * [3.1. Metadata URL](#auth-server-metadata-url)  
    * [3.2. Metadata Object Format](#auth-server-metadata-format)  
    * [3.3. Scope Descriptions Object Format](#scope-descriptions-format)  
    * [3.4. Registration Field Object Format](#registration-field-format)  
    * [3.5. Registration Field Types](#registration-field-types)  
    * [3.6. Authorization Details Field Object Format](#auth-details-fields-format)  
    * [3.7. Authorization Details Field Types](#auth-details-field-types)  
* [4. Client Registration Process](#client-registration-process)  
    * [4.1. Client Registration Request](#registration-request)  
    * [4.2. Client Registration Response](#registration-response)  
* [5. Clients API](#clients-api)  
    * [5.1. Client Object Format](#client-format)  
    * [5.2. Listing Clients](#clients-list)  
    * [5.3. Modifying Clients](#clients-modify)  
* [6. Client Settings API](#client-settings-api)  
    * [6.1. Client Settings Object Format](#client-settings-format)  
    * [6.2. Modifying Client Settings](#clients-settings-modify)  
* [7. Client Updates API](#client-updates-api)  
    * [7.1. Client Update Object Format](#client-update-format)  
    * [7.2. Client Update Types](#client-update-types)  
    * [7.3. Client Update Statuses](#client-update-statuses)  
    * [7.4. Client Update Request Object Format](#client-update-request-format)  
    * [7.5. Listing Client Updates](#clients-updates-list)  
    * [7.6. Modifying Client Updates](#clients-updates-modify)  
* [8. Scope Credentials API](#scope-creds-api)  
    * [8.1. Scope Credentials Object Format](#scope-creds-format)  
    * [8.2. Scope Credentials Statuses](#scope-creds-statuses)  
    * [8.3. Listing Scope Credentials](#scope-creds-list)  
    * [8.4. Creating Scope Credentials](#scope-creds-create)  
    * [8.5. Modifying Scope Credentials](#scope-creds-modify)  
    * [8.6. Deleting Scope Credentials](#scope-creds-delete)  
* [9. Grants API](#grants-api)  
    * [9.1. Grant Object Format](#grant-format)  
    * [9.2. Grant Statuses](#grant-statuses)  
    * [9.3. Listing Grants](#grants-list)  
    * [9.4. Modifying Grants](#grants-modify)  
* [10. Extensions](#extensions)  
* [11. Examples](#examples)  
* [12. Security Considerations](#security)  
    * [12.1. Restricted Access](#restricted-access)  
    * [12.2. Rate Limiting](#rate-limiting)  
* [13. References](#references)  
* [14. Acknowledgments](#acknowledgments)  
* [15. Authors' Addresses](#authors-addresses)  

## 1. Introduction <a id="introduction" href="#introduction" class="permalink">🔗</a>

This specification was developed as part of the global effort to combat the climate crisis. Specifically, in order to scalably measure carbon emissions of organizations and calculate the impact of deploying and operating clean energy technologies, companies need an efficient means to register and interact with energy utilities and other similar entities.

There are thousands of utilities serving customers across the world, and each have their own way of providing access to privileged functionality and data. This specification defines a way for these utilities and other entities ("Servers") to provide a standardized, structured process for external users and companies ("Clients") to register and obtain secure access to privileged functionality and data. By offering a standardized Client registration and management protocol, utilities and other entities can enable secure interoperability with other external systems providing climate-change fighting services such as carbon tracking, decarbonization measures, grid flexibility capabilities, and energy benchmarking.

This specification does NOT describe what specific privileged functionality or data is offered to Clients as part of their registration. Instead, relevant stakeholders are encouraged to write [Extensions](#extensions) to this specification that enable various use cases that need access to privileged functionality or data. For example, this specification may be extended to define a method for Clients to be able to access customer-authorized energy usage data.

## 2. Terminology <a id="terminology" href="#terminology" class="permalink">🔗</a>

<a id="server" href="#server" class="permalink">🔗</a> **"Server"** - The entity hosting the specified endpoints. A Server can be an energy utility or another type of entity that want to provide access to privileged functionality or data. These entities can include, but are not limited to, distribution utilities, grid operators, electric retailers, community choice aggregators, government agencies, data warehouses, and private infrastructure providers.

<a id="client" href="#client" class="permalink">🔗</a> **"Client"** - The entity requesting Server's metadata endpoints. A Client can be any organization or user seeking to register with a Server for a specific scope of access. These entities can include, but are not limited to, carbon tracking applications, electric vehicle companies, clean energy technology providers, commercial utility customers, grid management applications, and energy efficiency organizations.

<a id="referenced-technologies" href="#referenced-technologies" class="permalink">🔗</a> Referenced Technologies: "[HTTPS](https://www.rfc-editor.org/rfc/rfc9110#name-https-uri-scheme)", "[URL](https://www.rfc-editor.org/rfc/rfc3986.html#section-1.1.3)", "[Request Methods](https://www.rfc-editor.org/rfc/rfc9110#name-methods)", "[Status Codes](https://www.rfc-editor.org/rfc/rfc9110#name-status-codes)", "[JSON](https://www.rfc-editor.org/rfc/rfc8259)", "[OAuth](https://oauth.net/specs/)", <span style="background-color:yellow">TODO: add more as needed</span> are defined by their referenced standards documents.

<a id="key-words" href="#key-words" class="permalink">🔗</a> Key Words: "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" are defined in accordance with [BCP 14](https://www.rfc-editor.org/info/bcp14).


## 3. Authorization Server Metadata <a id="auth-server-metadata" href="#auth-server-metadata" class="permalink">🔗</a>

To enable automated Client registration, Servers MUST host an endpoint with structured metadata about how to submit a [registration request](#registration-request), as well as functionality and data capabilities offered by the Server. The requirements for hosting this metadata endpoint follows [OAuth 2.0 Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414) with several additions for Carbon Data Specification (CDS) functionality described in the following sections.

### 3.1. Metadata URL <a id="auth-server-metadata-url" href="#auth-server-metadata-url" class="permalink">🔗</a>

Following OAuth's [Obtaining Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414#section-3) process, a Server's Authorization Server Metadata Object is retrieved by making an HTTPS request to a specified URL.

To ensure discoverability to the specified URL, this specification extends [CDSC-WG1-01 Server Capabilities](/specs/cdsc-wg1-01/#server-capabilities) to require the following values:

* `oauth` - _string_ - (REQUIRED) Including Client Registration via OAuth in the list of a Server's available capabilities

As well as extending the [CDSC-WG1-01 Metadata Object Format](/specs/cdsc-wg1-01/#metadata-object-format) to require the following values:

* `oauth_metadata` - _URL_ - (REQUIRED) Where Clients can obtain the Server's Authorization Server Metadata Object

In addition to requiring that the URL be included in the [CDSC-WG1-01 Metadata Object Format](/specs/cdsc-wg1-01/#metadata-object-format), Servers MAY also configure the URL such that it conforms to the default [Well-Known URI](#https://www.rfc-editor.org/rfc/rfc8414#section-7.3) format, which can increase interoperability beyond the above specified CDSC-WG1-01 extension.

### 3.2. Metadata Object Format <a id="auth-server-metadata-format" href="#auth-server-metadata-format" class="permalink">🔗</a>

A Server's Authorization Server Metadata Object follows OAuth's [Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414#section-2) object format with the following modifications from OPTIONAL or RECOMMENDED to REQUIRED:

* `registration_endpoint` - _URL_ - (REQUIRED) OAuth's [Dynamic Client Registration](https://www.rfc-editor.org/rfc/rfc7591) functionality is required to enable the [Client Registration Process](#client-registration-process)
* `scopes_supported` -  _Array[string]_ - (REQUIRED) Disclosure of available scopes is required to enable integration capabilities into other platforms
* `service_documentation` - _URL_ - (REQUIRED) Developer documentation is required by the Server to help streamline Client integration
* `op_policy_uri` - _URL_ - (REQUIRED) Policies for Clients registering is required
* `op_tos_uri` - _URL_ - (REQUIRED) Terms of use for Clients registering is required
* `revocation_endpoint` - _URL_ - (REQUIRED) OAuth's [Token Revocation](https://www.rfc-editor.org/rfc/rfc7009) functionality is required
* `introspection_endpoint` - _URL_ - (REQUIRED) OAuth's [Token Introspection](https://www.rfc-editor.org/rfc/rfc7662) functionality is required
* `code_challenge_methods_supported` - _Array[string]_ - (REQUIRED) OAuth's [Proof Key for Code Exchange by OAuth Public Clients](https://www.rfc-editor.org/rfc/rfc7636) functionality is required. Servers MUST offer `S256` and MUST NOT offer `plain` code verifier methods.

In addition to the standard set of OAuth [Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414#section-2) values, this specification also requires the following OAuth extension capabilities to be included in the metadata object values:

* `authorization_details_types_supported` - _Array[string]_ - (REQUIRED) OAuth's [Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396) functionality is required. This array of values MUST be the same as `scopes_supported`.
* `pushed_authorization_request_endpoint` - _URL_ - (REQUIRED) OAuth's [Pushed Authorization Requests](https://www.rfc-editor.org/rfc/rfc9126) functionality is required

In addition to OAuth capabilities included in the metadata object, this specification adds the following Carbon Data Specification (CDS) values:

* `cds_oauth_version` - _string_ - (REQUIRED) The version of the CDS-WG1-02 Client Registration specification that the Server has implemented, which for this version of the specification is `v1`
* `cds_human_registration` - _URL_ - (REQUIRED) Where Clients who do not have the technical capacity to use the `registration_endpoint` can visit to manually register a Client using a web browser
* `cds_client_directory` - _URL_ - (REQUIRED) Where the Server hosts a webpage listing the Clients who have registered and been approved to be publicly listed as registered
* `cds_test_accounts` - _URL_ - (REQUIRED) Where Clients can find developer documentation on what test account credentials may be used for testing OAuth `response_type=code` authorization requests
* `cds_clients_api` - _URL_ - (REQUIRED) The base url for the [Clients API](#clients-api)
* `cds_client_settings_api` - _URL_ - (REQUIRED) The base url for the [Client Settings API](#client-settings-api)
* `cds_client_updates_api` - _URL_ - (REQUIRED) The base url for the [Client Updates API](#client-updates-api)
* `cds_scope_credentials_api` - _URL_ - (REQUIRED) The base url for the [Scope Credentials API](#scope-creds-api)
* `cds_grants_api` - _URL_ - (REQUIRED) The base url for the [Grants API](#grants-api)
* `cds_scope_descriptions` - _Hash[[ScopeDescription](#scope-descriptions-format)]_ - (REQUIRED) An object providing additional information about what Scope values listed in `scopes_supported` mean. This object MUST include a key for each Scope listed in `scopes_supported` with a [Scope Description](#scope-descriptions-format) as that key's value.
* `cds_registration_fields` - _Hash[[RegistrationField](#registration-field-format)]_ - (REQUIRED) An object providing additional information about Registration Field that are referenced in [Scope Description's](#scope-descriptions-format) `registration_requirements` list. This object MUST include a key for each Registration Field `id` included in the `registration_requirements` lists in the metadata object with a [Registration Field](#registration-field-format) as that key's value. If all `registration_requirements` lists are empty, this reference object is an empty object (`{}`).

Other values in specifications for the Authorization Server Metadata specification or other extensions MAY be included as described in their respective specifications.

### 3.3. Scope Descriptions Object Format <a id="scope-descriptions-format" href="#scope-descriptions-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 3.4. Registration Field Object Format <a id="registration-field-format" href="#registration-field-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 3.5. Registration Field Types <a id="registration-field-types" href="#registration-field-types" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 3.6. Authorization Details Field Object Format <a id="auth-details-fields-format" href="#auth-details-fields-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 3.7. Authorization Details Field Types <a id="auth-details-field-types" href="#auth-details-field-types" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 4. Client Registration Process <a id="client-registration-process" href="#client-registration-process" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 4.1. Client Registration Request <a id="registration-request" href="#registration-request" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 4.2. Client Registration Response <a id="registration-response" href="#registration-response" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 5. Clients API <a id="clients-api" href="#clients-api" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 5.1. Client Object Format <a id="client-format" href="#client-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 5.2. Listing Clients <a id="clients-list" href="#clients-list" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 5.3. Modifying Clients <a id="clients-modify" href="#clients-modify" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 6. Client Settings API <a id="client-settings-api" href="#client-settings-api" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 6.1. Client Settings Object Format <a id="client-settings-format" href="#client-settings-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 6.2. Modifying Client Settings <a id="clients-settings-modify" href="#clients-settings-modify" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 7. Client Updates API <a id="client-updates-api" href="#client-updates-api" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 7.1. Client Update Object Format <a id="client-update-format" href="#client-update-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 7.2. Client Update Types <a id="client-update-types" href="#client-update-types" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 7.3. Client Update Statuses <a id="client-update-statuses" href="#client-update-statuses" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 7.4. Client Update Request Object Format <a id="client-update-request-format" href="#client-update-request-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 7.5. Listing Client Updates <a id="clients-updates-list" href="#clients-updates-list" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 7.6. Modifying Client Updates <a id="clients-updates-modify" href="#clients-updates-modify" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 8. Scope Credentials API <a id="scope-creds-api" href="#scope-creds-api" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 8.1. Scope Credentials Object Format <a id="scope-creds-format" href="#scope-creds-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 8.2. Scope Credentials Statuses <a id="scope-creds-statuses" href="#scope-creds-statuses" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 8.3. Listing Scope Credentials <a id="scope-creds-list" href="#scope-creds-list" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 8.4. Creating Scope Credentials <a id="scope-creds-create" href="#scope-creds-create" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 8.5. Modifying Scope Credentials <a id="scope-creds-modify" href="#scope-creds-modify" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 8.6. Deleting Scope Credentials <a id="scope-creds-delete" href="#scope-creds-delete" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 9. Grants API <a id="grants-api" href="#grants-api" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 9.1. Grant Object Format <a id="grant-format" href="#grant-format" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 9.2. Grant Statuses <a id="grant-statuses" href="#grant-statuses" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 9.3. Listing Grants <a id="grants-list" href="#grants-list" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 9.4. Modifying Grants <a id="grants-modify" href="#grants-modify" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 10. Extensions <a id="extensions" href="#extensions" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 11. Examples <a id="examples" href="#examples" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 12. Security Considerations <a id="security" href="#security" class="permalink">🔗</a>

This specification describes a protocol by which a utility or other entity (a Server) can allow third parties (Clients) to register and obtain privileged access to the Server's offered capabilities and data. Because the functionality described in this specification enables access to private Server functionality and data, Servers MUST follow industry cybersecurity best practices when securing their implementations of this specification to prevent unintended or inadvertent access to privileged functionality or data to Clients who are not authorized. These best practices include requiring HTTPS for API endpoints using the latest widely adopted encryption standards, undergoing regular security audits and penetration tests, and internally requiring security-focused process controls and data handling procedures.

### 12.1. Restricted Access <a id="restricted-access" href="#restricted-access" class="permalink">🔗</a>

For unauthenticated endpoints ([Authorization Server Metadata](#auth-server-metadata), [Client Registration Process](#client-registration-process)), while Servers can add [rate limiting](#rate-limiting) configurations to protect their systems from being overwhelmed with requests, Servers MUST NOT add anti-bot blocking measures (e.g. captchas) that prevent automated requests from other systems. The functionality described in this specification is intended to be able to be integrated in other platforms to allow those platforms to automate interactions with Servers on their users' behalf.

If [Authorization Server Metadata](#auth-server-metadata) is referenced in a public [CDSC-WG1-01 Server Metadata endpoint](/specs/cdsc-wg1-01/#metadata-endpoint), then the [Authorization Server Metadata](#auth-server-metadata) and [Client Registration Process](#client-registration-process) must also be public.

Servers that wish to restrict access of by-default unauthenticated endpoints to certain Clients MUST configure well established authentication processes for Clients to ensure that only the approved Clients may access the restricted endpoint. This specification does not describe specifically how Servers will authenticate Clients for by-default unauthenticated endpoints, as these restricted access protocols are context dependent. For example, if a Server providing a private Client Registration endpoint as part of an existing logged in portal, then they can use that logged in portal's session cookie to authenticate Client requests to the registration endpoint.

For authenticated endpoints ([Clients API](#clients-api), [Client Settings API](#client-settings-api), [Client Updates API](#client-updates-api), [Scope Credentials API](#scope-creds-api), [Grants API](#grants-api)), Servers MUST authenticate requests using OAuth's [Authorization Request Header Field](https://www.rfc-editor.org/rfc/rfc6750#section-2.1) with access tokens obtained using the OAuth's [Issuing an Access Token](https://www.rfc-editor.org/rfc/rfc6749#section-5) process.

### 12.2. Rate Limiting <a id="rate-limiting" href="#rate-limiting" class="permalink">🔗</a>

For unauthenticated endpoints ([Authorization Server Metadata](#auth-server-metadata), [Client Registration Process](#client-registration-process)), Servers SHOULD configure rate limiting restrictions so that bots and misconfigured scripts will not flood and overwhelm the endpoints with requests, while still allowing legitimate and low-volume automated requests have access to the endpoints.

For authenticated endpoints ([Clients API](#clients-api), [Client Settings API](#client-settings-api), [Client Updates API](#client-updates-api), [Scope Credentials API](#scope-creds-api), [Grants API](#grants-api)), Servers SHOULD configure rate limiting by Client and Scope Credential to ensure that individual Clients do not overwhelm Servers with authenticated API requests. Additionally, Servers SHOULD configure rate limiting for unauthenticated or failed authentication requests to authenticated API endpoints to prevent brute force attempts to gain access to authenticated APIs.

## 13. References <a id="references" href="#references" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## 14. Acknowledgments <a id="acknowledgments" href="#acknowledgments" class="permalink">🔗</a>

The authors would like to thank the late Shuli Goodman, who was the Executive Director of LFEnergy, for her incredible leadership in initially organizing the Carbon Data Specification.

## 15. Authors' Addresses <a id="authors-addresses" href="#authors-addresses" class="permalink">🔗</a>

Daniel Roesler  
UtilityAPI  
Email: [daniel@utilityapi.com](mailto:daniel@utilityapi.com)  
URI: [https://utilityapi.com/](https://utilityapi.com/)

