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
    * [3.6. Registration Field Formats](#registration-field-formats)  
    * [3.7. Authorization Details Field Object Format](#auth-details-fields-format)  
    * [3.8. Authorization Details Field Formats](#auth-details-field-formats)  
* [4. Client Registration Process](#client-registration-process)  
    * [4.1. Client Registration Request](#registration-request)  
    * [4.2. Client Registration Response](#registration-response)  
* [5. Clients API](#clients-api)  
    * [5.1. Client Object Format](#client-format)  
    * [5.2. Listing Clients](#clients-list)  
    * [5.3. Modifying Clients](#clients-modify)  
* [6. Client Settings API](#client-settings-api)  
    * [6.1. Client Settings Object Format](#client-settings-format)
    * [6.2. Profile Visibilities](#profile-visibilities)  
    * [6.3. Button Styles](#button-styles)  
    * [6.4. Modifying Client Settings](#clients-settings-modify)  
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
* `client_directory` - _URL_ - (REQUIRED) Where users may view a publicly available directory of Clients who have registered and set their Client Settings `profile_visibility` to `listed`

In addition to requiring that the URL be included in the [CDSC-WG1-01 Metadata Object Format](/specs/cdsc-wg1-01/#metadata-object-format), Servers MAY also configure the URL such that it conforms to the default [Well-Known URI](#https://www.rfc-editor.org/rfc/rfc8414#section-7.3) format, which can increase interoperability beyond the above specified CDSC-WG1-01 extension.

### 3.2. Metadata Object Format <a id="auth-server-metadata-format" href="#auth-server-metadata-format" class="permalink">🔗</a>

A Server's Authorization Server Metadata Object follows OAuth's [Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414#section-2) object format with the following modifications from OPTIONAL or RECOMMENDED to REQUIRED:

* `registration_endpoint` - _URL_ - (REQUIRED) OAuth's [Dynamic Client Registration](https://www.rfc-editor.org/rfc/rfc7591) functionality is required to enable the [Client Registration Process](#client-registration-process).
* `scopes_supported` -  _Array[string]_ - (REQUIRED) Disclosure of available scopes is required to enable integration capabilities into other platforms. This array MUST contain the `client_admin` scope.
* `service_documentation` - _URL_ - (REQUIRED) Developer documentation is required by the Server to help streamline Client integration.
* `op_policy_uri` - _URL_ - (REQUIRED) Policies for Clients registering is required.
* `op_tos_uri` - _URL_ - (REQUIRED) Terms of use for Clients registering is required.
* `revocation_endpoint` - _URL_ - (REQUIRED) OAuth's [Token Revocation](https://www.rfc-editor.org/rfc/rfc7009) functionality is required.
* `introspection_endpoint` - _URL_ - (REQUIRED) OAuth's [Token Introspection](https://www.rfc-editor.org/rfc/rfc7662) functionality is required.
* `code_challenge_methods_supported` - _Array[string]_ - (REQUIRED) OAuth's [Proof Key for Code Exchange by OAuth Public Clients](https://www.rfc-editor.org/rfc/rfc7636) functionality is required. Servers MUST offer `S256` and MUST NOT offer `plain` code verifier methods.

In addition to the standard set of OAuth [Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414#section-2) values, this specification also requires the following OAuth extension capabilities to be included in the metadata object values:

* `authorization_details_types_supported` - _Array[string]_ - (REQUIRED) OAuth's [Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396) functionality is required. This array of values MUST be the same as `scopes_supported`.
* `pushed_authorization_request_endpoint` - _URL_ - (REQUIRED) OAuth's [Pushed Authorization Requests](https://www.rfc-editor.org/rfc/rfc9126) functionality is required.

In addition to the above additionally required set of OAuth [Authorization Server Metadata](https://www.rfc-editor.org/rfc/rfc8414#section-2) values, this specification clarifies use of the following OAuth sandard values:

* `response_types_supported` - _Array[string]_ - (REQUIRED) The response types in this array MUST represent a union of all `response_types_supported` values contained in the `cds_scope_descriptions` object.
* `grant_types_supported` - _Array[string]_ - (REQUIRED) The response types in this array MUST represent a union of all `grant_types_supported` values contained in the `cds_scope_descriptions` object.
* `token_endpoint_auth_methods_supported` - _Array[string]_ - (REQUIRED) The response types in this array MUST represent a union of all `token_endpoint_auth_methods_supported` values contained in the `cds_scope_descriptions` object.

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
* `cds_scope_descriptions` - _Map[[ScopeDescription](#scope-descriptions-format)]_ - (REQUIRED) An object providing additional information about what Scope values listed in `scopes_supported` mean. This object MUST include a key for each Scope listed in `scopes_supported` with a [Scope Description](#scope-descriptions-format) as that key's value.
* `cds_registration_fields` - _Map[[RegistrationField](#registration-field-format)]_ - (REQUIRED) An object providing additional information about Registration Field that are referenced in [Scope Description's](#scope-descriptions-format) `registration_requirements` list. This object MUST include a key for each Registration Field `id` included in the `registration_requirements` lists in the metadata object with a [Registration Field](#registration-field-format) as that key's value. If all `registration_requirements` lists are empty, this reference object is an empty object (`{}`).

Other values not mentioned here but listed in specifications for the Authorization Server Metadata specification or other extensions MAY be included as described in their respective specifications.

### 3.3. Scope Descriptions Object Format <a id="scope-descriptions-format" href="#scope-descriptions-format" class="permalink">🔗</a>

Scope Description objects are formatted as JSON objects and contain named values. The following values are included in the default list available in scope description objects.

* `id` - _string_ - (REQUIRED) The unique identifier of the scope. This MUST be the same value as the object key the Metadata Object's `cds_scope_descriptions` object.
* `name` - _string_ - (REQUIRED) A human-readable name of the scope.
* `description` - _string_ - (REQUIRED) A human-readable description of what access or actions the scope will enable.
* `documentation` - _URL_ - (REQUIRED) Where developers can find documentation for the scope.
* `registration_requirements` - _Array[string]_ - (REQUIRED) A list of any registration fields that MUST be submitted with [Client Registration Requests](#registration-request) or requirements that must be completed after registration before the scope is available to use in the Server's production environment. All values in this array MUST be included in the Metadata Object's `cds_registration_fields` object for a complete description of the requirement. If no additional requirements are needed, this value is an empty array (`[]`).
* `registration_optional` - _Array[string]_ - (REQUIRED) A list of any registration fields that Clients MAY submit with the [Client Registration Requests](#registration-request). If no additional fields are available, this value is an empty array (`[]`).
* `response_types_supported` - _Array[string]_ - (REQUIRED) The OAuth `response_type` values that can be used with this scope. This value follows the same behavior as OAuth's [Metadata object `response_types_supported`](https://www.rfc-editor.org/rfc/rfc8414#section-2), except that this value represents the response types supported for this individual scope and not all scopes for the Client. If a scope is not available for OAuth authorization requests (e.g. a `client_credentials`-only scope), this value is an empty array (`[]`).
* `grant_types_supported` - _Array[string]_ - (REQUIRED) The OAuth grant type values that can be used with this scope. This value follows the same behavior as OAuth's [Metadata object `grant_types_supported`](https://www.rfc-editor.org/rfc/rfc8414#section-2), except that this value represents the grant types supported for this individual scope and not all scopes for the Client. This array MUST contain at least one grant type.
* `token_endpoint_auth_methods_supported` - _Array[string]_ - (REQUIRED) The OAuth client authentication methods that can be used for granting tokens with this scope. This value follows the same behavior as OAuth's [Metadata object `token_endpoint_auth_methods_supported`](https://www.rfc-editor.org/rfc/rfc8414#section-2), except that this value represents the client authentication methods supported for this individual scope and not all scopes for the Client. A value of `"none"` this array indicates that the scope may be used for public application without using a client secret.
* `authorization_details_fields` - _Array[[AuthorizationDetailsField](#auth-details-fields-format)]_ - (REQUIRED) A list of fields that MAY be included in [Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396) the authorization details object for this scope `type`. If no extra authorization details fields are available, this value is an empty list (`[]`).

### 3.4. Registration Field Object Format <a id="registration-field-format" href="#registration-field-format" class="permalink">🔗</a>

Registration Field objects are formatted as JSON objects and contain named values. The following values are included in the default list available in registration fields objects.

* `id` - _string_ - (REQUIRED) The unique identifier of the registration field. This MUST be the same value as the object key the Metadata Object's `cds_registration_fields` object.
* `type` - _[RegistrationFieldType](#registration-field-types)_ - (REQUIRED) What type of Registration Field this entry is.
* `description` - _string_ - (REQUIRED) A human-readable description of what should be submitted or expected for this registration field.
* `documentation` - _URL_ - (REQUIRED) Where developers can find more information about this registration field.
* `field_name` - _string_ - (OPTIONAL) If type is `registration_field`, this is the name of the field to submit in the [Client Registration Request](#registration-request) and MUST start with `cds_`.
* `format` - _[RegistrationFieldFormats](#registration-field-formats)_ - (OPTIONAL) If type is `registration_field`, this is the data format that MUST be used in the value of the field.
* `default` - _various_ - (OPTIONAL) If type is `registration_field` and the field is optional, this is the default value that will be used in lieu of the Client submitting a value themselves. Including this `default` value in the object indicates the registration field is optional.
* `max_length` - _integer_ - (OPTIONAL) If format is one of `string`, `string_or_null`, `url`, `url_or_null`, `email`, or `email_or_null`, this is the maximum length of the submitted value, if not `null`.
* `max_size` - _integer_ - (OPTIONAL) If format is one of `image`, `image_or_null`, `pdf`, or `pdf_or_null`, this is the maximum file size of the submitted value before encoding into [Base64](https://en.wikipedia.org/wiki/Base64), if not `null`.
* `amount` - _decimal_ - (OPTIONAL) If type is `payment_required`, this is the amount in `currency` that will be required to complete registration.
* `currency` - _string_ - (OPTIONAL) If type is `payment_required`, this is the monetary currency in [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) currency code.

### 3.5. Registration Field Types <a id="registration-field-types" href="#registration-field-types" class="permalink">🔗</a>

Registration Field types define which type of action the Client or Server will take for that registration field. Registration Fields can be either something that MUST or MAY be submitted as part of the [Client Registration Request](#registration-request), depending on the submitted scope's `registration_requirements` and `registration_optional` values, or can be something that a Client must do after registration in order to be full approved for use in the production environment.

The following list of strings are an enumerated set of registration field types that are valid `type` values in the registration field object.

* `registration_field` - This field should be submitting with the [Client Registration Request](#registration-request) as the `field_name`. This type of registration field MUST also have `field_name` and `format` values. If this field is optional as part of registration, `default` must also be defined in the registration field object.
* `manual_review` - This field indicates that the Server will manually review the registration before approving it for production use. Notifications and communication about the status of this manual review will be conveyed using the [Client Updates API](#client-updates-api).
* `payment_required` - This field indicates that a setup payment will be required before the Server will approve the Client for production use. Notifications and communication about how to pay and confirmation of payment will be conveyed using the [Client Updates API](#client-updates-api).
* `email_verification` - This field indicates that the Client must verify their email before the Server will approve the Client for production use. Notifications and communication about how to verify the Client's contact email will be conveyed using the [Client Updates API](#client-updates-api).

### 3.6. Registration Field Formats <a id="registration-field-formats" href="#registration-field-formats" class="permalink">🔗</a>

Registration Field formats define the data type of submitted values for these fields in [Client Registration Requests](#registration-request).

The following list of strings are an enumerated set of registration field formats that are valid `format` values in the registration field object.

* `string` - If required, a string value MUST be submitted.
* `string_or_null` - Same as `string`, only with `null` being an additional possible value.
* `url` - If required, a URL value MUST be submitted.
* `url_or_null` - Same as `url`, only with `null` being an additional possible value.
* `email` - If required, a valid email address string MUST be submitted.
* `email_or_null` - Same as `email`, only with `null` being an additional possible value.
* `boolean` - If required, either `true` or `false` MUST be submitted.
* `boolean_or_null` - Same as `boolean`, only with `null` being an additional possible value.
* `image` - If required, a valid image file formatted as `image/jpeg` or `image/png` encoded as a string using [Base64](https://en.wikipedia.org/wiki/Base64) MUST be submitted.
* `image_or_null` - Same as `image`, only with `null` being an additional possible value.
* `pdf` - If required, a valid pdf file formatted encoded using [Base64](https://en.wikipedia.org/wiki/Base64) as a string MUST be submitted.
* `pdf_or_null` - Same as `pdf`, only with `null` being an additional possible value.

### 3.7. Authorization Details Field Object Format <a id="auth-details-fields-format" href="#auth-details-fields-format" class="permalink">🔗</a>

Authorization Details Field objects are formatted as JSON objects and contain named values. The following values are included in the default list available in authorization details field objects.

* `id` - _string_ - (REQUIRED) The unique identifier of the authorization details field. This value is used as the key for the field when added to `authorization_details` data fields as part of OAuth's [Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396).
* `name` - _string_ - (REQUIRED) A human-readable name of the authorization details field.
* `description` - _string_ - (REQUIRED) A human-readable description of what submitting values for this authorization details field means.
* `documentation` - _URL_ - (REQUIRED) Where developers can find more information about this authorization details field.
* `format` - _[AuthorizationDetailsFieldFormats](#auth-details-field-formats)_ - (OPTIONAL) The data format that MUST be used in the value of the field when including it as a data field in `authorization_details` objects.
* `default` - _various_ - (REQUIRED) The default value that will be used in lieu of the Client submitting a value themselves. This is also the value that will be used if a basic OAuth `scope` string parameter is used instead of an `authorization_details` parameter.
* `relative_date_limit` - _integer_ - (OPTIONAL) If `format` is `relative_or_absolute_date`, this is the largest relative date range that may be submitted. Servers MUST validate both submitted absolute dates and relative dates against the relative date limit, where when comparing to a submitted absolute date, the current Server date in the Server's local timezone is used as the relative point of reference.
* `absolute_date_limit` - _integer_ - (OPTIONAL) If `format` is `relative_or_absolute_date`, this is the furthest away date that may be submitted. Servers MUST validate both submitted absolute dates and relative dates against the absolute date limit, where when comparing to a submitted relative date, the current Server date in the Server's local timezone is used as the relative point of reference.
* `limit` - _integer_ - (OPTIONAL) If format is one of `int` or `decimal`, this is the largest value that can be the submitted.

### 3.8. Authorization Details Field Formats <a id="auth-details-field-formats" href="#auth-details-field-formats" class="permalink">🔗</a>

Authorization Details Field formats define the data type of submitted values for these fields in `authorization_details` for OAuth's [Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396).

The following list of strings are an enumerated set of authorization details field formats that are valid `format` values in the [Authorization Details Field objects](#auth-details-fields-format).

* `relative_or_absolute_date` - A relative or absolute date string. Relative dates are formatted as a positive integer followed by a `y`, `m`, `w`, or `d` character, where the character represents the unit of duration (year, month, week, and day), and the integer represents the number of units for that duration. For example, `3y` represents a relative date range of 3 years. Absolute dates are formatted as ISO8601 dates (`YYYY-MM-DD`). For example, `2024-01-02` represents the 2nd of January, 2024. Dates are defined as the date from the perspective of the Server's local timezone.
* `int` - An integer value.
* `decimal` - A decimal value, which can have any number of significant units, but MUST NOT be stored or handled as a float value, in order to retain the precision of the value throughout Server and Client processing.

## 4. Client Registration Process <a id="client-registration-process" href="#client-registration-process" class="permalink">🔗</a>

OAuth's [Dynamic Client Registration](https://www.rfc-editor.org/rfc/rfc7591) is used as the basis for initially registering Clients with Servers. To perform the dynamic registration process, the Client submits a request to the Server's `registration_endpoint` provided in the Server's [Metadata object](#auth-server-metadata-format) as described in [Client Registration Request](#registration-request). The Server then responds with either an error or a generated Client registration response as described in [Client Registration Response](#registration-response).

### 4.1. Client Registration Request <a id="registration-request" href="#registration-request" class="permalink">🔗</a>

This specification requires Clients and Servers follow the process described in OAuth's [Client Registration Request](https://www.rfc-editor.org/rfc/rfc7591#section-3.1), with the following modifications.

* Clients MAY submit additional named values that are defined as part of scope `registration_requirements` and `registration_optional` arrays in `cds_scope_descriptions` objects, using the registration field reference's `field_name` value as the submitted key in the registration request.

### 4.2. Client Registration Response <a id="registration-response" href="#registration-response" class="permalink">🔗</a>

This specification requires Servers follow the process described in OAuth's [Client Registration Response](https://www.rfc-editor.org/rfc/rfc7591#section-3.2), with the following modifications to OAuth's [Client Information Response](https://www.rfc-editor.org/rfc/rfc7591#section-3.2.1) object.

* A `client_secret` value MUST be present in the response and be limited to `client_admin` scope use only.
* The `scope` value MUST include the `client_admin` scope.
* The `redirect_uris`, `grant_types`, and `response_types` values, if included, MUST be dynamically determined by the Server as a union of all of their respective values in the Client's [Scope Credentials](#scope-creds-format).
* The `token_endpoint_auth_method` MUST always be set to `client_secret_basic`.

Additionally, the following additional named values MUST be included in the response.

* `cds_server_metadata` - _URL_ - (REQUIRED) Where the Client can find their registration-specific version of the [CDSC-WG1-01 Server Metadata](/specs/cdsc-wg1-01/). If the Client's CDSC server metadata is no different from the public CDSC server metadata, Servers MAY simply link to the public URL. If this metadata endpoint requires authentication, Servers MUST authenticate Client requests to this endpoint via Bearer access token obtained using OAuth's `client_credentials` grant with a scope of `client_admin`, and reject unauthenticated requests with a `401 Unauthorized` response code. Clients know that they must use a Bearer token when Servers return a `401` response code for this endpoint when the Client makes an unauthenticated request to the endpoint.
* `cds_clients_api` - _URL_ - (REQUIRED) The base url for the [Clients API](#clients-api).
* `cds_client_settings_api` - _URL_ - (REQUIRED) The url to a Client's Settings object for the [Client Settings API](#client-settings-api).
* `cds_client_updates_api` - _URL_ - (REQUIRED) The base url for the [Client Updates API](#client-updates-api).
* `cds_scope_credentials_api` - _URL_ - (REQUIRED) The base url for the [Scope Credentials API](#scope-creds-api).
* `cds_grants_api` - _URL_ - (REQUIRED) The base url for the [Grants API](#grants-api).

Upon valid registration, Servers MUST generate a [listed Scope Credential](#scope-creds-list) that is scoped to only `client_admin`, where the value of the Scope Credential's `client_secret` and the registration response `client_secret` are the same. Servers MUST also create [listed Scope Credentials](#scope-creds-list) that cover all additional valid scopes submitted, if any, where the Scope Credential objects are grouped by scopes that have the same `response_types`, `grant_types`, and `token_endpoint_auth_method` so as to maximize the number of scopes that may be accessed using the same `client_secret`.

Severs MUST assign the submitted `redirect_uris` to all [Scope Credentials](#scope-creds-format) in the [Scope Credentials API](#scope-creds-api) that have `response_types`. Clients MAY subsequently manage which `redirect_uris` values are assigned to specific Scope Credentials using the [Scope Credentials API](#scope-creds-api).

While Servers MAY support OAuth's [Dynamic Client Registration Management Protocol](https://www.rfc-editor.org/rfc/rfc7592) by including the `registration_client_uri` and `registration_access_token` values in their registration response, this specification requires that Servers MUST support the [Clients API](clients-api) by including the `cds_clients_api` value in the registration response as a way for Clients to manage their registrations.

## 5. Clients API <a id="clients-api" href="#clients-api" class="permalink">🔗</a>

This specification requires Servers provide a set of Application Programming Interfaces (APIs) allowing Clients to view and edit their Client registrations. These APIs are authenticated using a Bearer `access_token` granted to Clients using OAuth's [`client_credentials` grant](https://www.rfc-editor.org/rfc/rfc6749#section-4.4) and the `client_secret` provided in the initial [Client Registration Response](#registration-response) or `client_secret` values from the [Scope Credentials API](#scope-creds-api) that include the `client_admin` scope.

### 5.1. Client Object Format <a id="client-format" href="#client-format" class="permalink">🔗</a>

Client objects are formatted as JSON objects and contain named values. Client objects are required to follow the same object format as the [Client Registration Response](#registration-response), with the addition of the following named values.

* `cds_client_uri` - _URL_ - (REQUIRED) Where to submit modifications using the Clients API [Modifying Clients](#clients-modify) functionality.

Additionally, while the [Client Registration Response](#registration-response) MUST contain the `client_secret` field. Client objects returned from the Clients API MUST NOT include the `client_secret` or `client_secret_expires_at` fields. Clients MUST instead use the [Scope Credentials API](#scope-creds-api) to obtain `client_secret` values for use in other APIs.

### 5.2. Listing Clients <a id="clients-list" href="#clients-list" class="permalink">🔗</a>

Clients may request to list Client objects that they have access to by making an HTTPS GET request, authenticated with a valid Bearer `access_token` scoped to the `client_admin` scope, to the `cds_clients_api` URL included in the [Client Registration Response](#registration-response). The Client listing request responses are formatted as JSON objects and contain the following named values.

* `clients` - _Array[[ClientObject](#client-format)]_ - (REQUIRED) A list of Clients to which the requesting `access_token` is scoped to have access. If no Clients are accessible, this value is an empty list (`[]`). If more than 100 Clients are available to be listed, Servers MAY truncate the list and use the `next` value to link to the next segment of the list of Clients.
* `next` - _URL_ or `null` - Where to request the next segment of the list of Clients. If no next segment exists (i.e. the requester is at the end of the list), this value is `null`.
* `previous` - _URL_ or `null` - Where to request the previous segment of the list of Clients. If no previous segment exists (i.e. the requester is at the front of the list), this value is `null`.

### 5.3. Modifying Clients <a id="clients-modify" href="#clients-modify" class="permalink">🔗</a>

This specification requires that the procedure to modify Clients MUST follow OAuth's [Client Update Request](https://www.rfc-editor.org/rfc/rfc7592) section in [Dynamic Client Registration Management Protocol](https://www.rfc-editor.org/rfc/rfc7592).

The URL to be used to send `PUT` requests for updating clients MUST be the `cds_client_uri` provided in the [Client object](#client-format). If a Server has optionally implemented OAuth's [Dynamic Client Registration Management Protocol](https://www.rfc-editor.org/rfc/rfc7592), the value of `cds_client_uri` MUST be the same as `registration_client_uri`, and access tokens issued from either a `client_credentials` grant with the scope `client_admin` or the access token provided as the `registration_access_token` MUST be valid access tokens to interact with the client update endpoint.

Servers MUST ignore updated values to the following fields and keep the Server-defined values set:

* `client_id` - This value is immutable from when it was assigned upon Client registration.
* `client_id_issued_at` - This value is immutable from when the Client initially registered.
* `client_secret` - This value is NOT included in any Client object response, except for the initial Client registration request. Clients MUST use the [Scope Credentials API](#scope-creds-api) to manage client secrets.
* `client_secret_expires_at` - This value is NOT included in any Client object response, except for the initial Client registration request. Clients MUST use the [Scope Credentials API](#scope-creds-api) to discover when a client secret will expire.
* `redirect_uris` - This list is dynamically determined by the Server as a union of all `redirect_uris` values in the Client's [Scope Credentials](#scope-creds-format).
* `grant_types` - This list is dynamically determined by the Server as a union of all `grant_types` values in the Client's [Scope Credentials](#scope-creds-format).
* `response_types` - This list is dynamically determined by the Server as a union of all `response_types` values in the Client's [Scope Credentials](#scope-creds-format).
* `token_endpoint_auth_method` - Always `client_secret_basic`.
* `cds_server_metadata` - This is a URL set by the Server.
* `cds_clients_api` - This is a URL set by the Server.
* `cds_client_settings_api` - This is a URL set by the Server.
* `cds_client_updates_api` - This is a URL set by the Server.
* `cds_scope_credentials_api` - This is a URL set by the Server.
* `cds_grants_api` - This is a URL set by the Server.

All other fields MAY be submitted for updating by the Client. However, the Server MUST determine the validity of the submitted Client fields and reject with a 400 Bad Response if not all submitted fields are either the same as previously set, or invalid values for that field.

If a Client does not include a field that is included in the Client object, this indicates that the Client wishes to reset the value of that field to the Server default.

If a Server needs to asynchronously review and approve changes to any submitted Client object fields that have been submitted by the Client and are different from the current values, for valid update requests the Server MUST respond with a `202 Accepted` response, which indicates that the submission was accepted but not fully saved as completed yet. Any fields that have not been synchronously updated as part of the request and response MUST remain in the response as their previous values, and the Server MUST add one or more entries to the [listed Client Updates](#clients-updates-list) for the modified fields that need to be asynchronously reviewed and approved. Clients MAY then use the [Client Updates API](#client-updates-api) to track the asynchronous review of the modification request. If all submitted fields have been synchronously updated as part of the response, Servers MUST respond with a `200 OK`  response.

## 6. Client Settings API <a id="client-settings-api" href="#client-settings-api" class="permalink">🔗</a>

This specification requires Servers provide a set of APIs allowing Clients to view and manage their Client settings. While the settings fields are directly related to a specific registered Client, they are not included in the Client object itself in order to allow Clients to make partial updates to their settings values using an HTTP PATCH request instead of a PUT request, which is required for the Client object updates to remain compatible with OAuth's [Dynamic Client Registration Management Protocol](https://www.rfc-editor.org/rfc/rfc7592).

Like Clients APIs, the Client Settings APIs are authenticated using a Bearer `access_token` granted to Clients using OAuth's [`client_credentials` grant](https://www.rfc-editor.org/rfc/rfc6749#section-4.4) and the `client_secret` provided in the initial [Client Registration Response](#registration-response) or `client_secret` values from the [Scope Credentials API](#scope-creds-api) that include the `client_admin` scope.

### 6.1. Client Settings Object Format <a id="client-settings-format" href="#client-settings-format" class="permalink">🔗</a>

Client Settings objects are formatted as JSON objects and contain the following named values:

* `default_scope` - _string_ - (REQUIRED) A space-separated list of scopes the Server will use for OAuth's [Authorization Requests](https://www.rfc-editor.org/rfc/rfc6749#section-4.1.1) if the Client does not supply a `scope` or `authorization_details` parameter in the request.
* `profile_visibility` - _[ProfileVisibility](#profile-visibilities)_ - (REQUIRED) Servers MUST provide a publicly accessible directory web interface of Clients that wish to be listed as registered with the Server. This setting is for Clients to manage the visibility of their registration in the public directly.
* `profile_visibility_options` - _Array[[ProfileVisibility](#profile-visibilities)]_ - (REQUIRED) This is the list of available Profile Visibility values that the Client may choose to set as their `profile_visiblity`.
* `profile_description` - _string_ - (REQUIRED) This is the description that the Client wishes to be displayed in their public directory entry, if `profile_visibility` is set to `autolist`, `unlisted`, or `listed`.
* `profile_uri` - _URL_ - (REQUIRED) This is the URL that links directly to the Client's entry in the public directory, if `profile_visibility` is set to `autolist`, `unlisted`, or `listed`. If a Client has set their `profile_visibility` to `disabled`, the Server MUST respond to requests to this URL with a 404 Not Found response code.
* `profile_slug` - _string_ - (OPTIONAL) For Servers that have the ability to partially customize the `profile_uri` string to be a more human-readable URL, this is the part of the URL that is customizeable by the Client.
* `profile_button_uri` - _URL or `null`_ - (REQUIRED) In the profile listing for a Client, Servers MUST offer the ability for the Client to link users to a Client-provided URL. Servers MUST implement this as an anchor link styled as one of the specified [Button Styles](#button-styles) that opens a new tab via the `target="_blank"` anchor attribute for the user. If a Client sets this value to `null`, then the Server MUST NOT render the button link in the Client's directory profile entry.
* `profile_button_style` - _[ButtonStyle](#button-styles)_ - (REQUIRED) This is the style of button the Server has set for the `profile_button_uri`.
* `profile_button_style_options` - _Array[[ButtonStyle](#button-styles)]_ - (REQUIRED) This is a list of [Button Styles](#button-styles) available for the Client to set as their button style.
* `profile_contact_name` - _string_ - (REQUIRED) This the contact name to be listed in the public directory entry for the Client.
* `profile_contact_email` - _[E.123](https://en.wikipedia.org/wiki/E.123) email address_ - (REQUIRED) This the contact email to be listed in the public directory entry for the Client.
* `profile_contact_phone` - _[E.123](https://en.wikipedia.org/wiki/E.123) international phone number or `null`_ - (REQUIRED) This the contact phone number to be listed in the public directory entry for the Client. If the Client sets this to `null`, the phone number field MUST be hidden on the Client's public directory profile entry by the Server.
* `profile_contact_website` - _URL or `null`_ - (REQUIRED) This is a link to a website the Client wishes the user to visit for more information on how to contact them. If the Client sets this to `null`, the website field MUST be hidden on the Client's public directory profile entry by the Server.

### 6.2. Profile Visibilities <a id="profile-visibilities" href="#profile-visibilities" class="permalink">🔗</a>

Servers MUST implement a publicly available directory web interface of Client profiles that wish to be listed. Servers MUST make available the following Profile Visibility options in the `profile_visibility_options` list:

* `listed` - The Client profile will be listed in the Server's public directory. If Servers are asynchronously reviewing Clients before approving production access to scopes, Servers MUST NOT include this option in the list of `profile_visibility_options` until the Server has completed and approve the Client for production access to at least one of the non-`client_admin` scopes, and instead include `autolist` in the list of `profile_visibility_options`.
* `autolist` - If Servers are asynchronously reviewing Clients before approving production access to scopes, Servers MUST include this option in the list of `profile_visibility_options` until the Server has completed and approve the Client for production access to at least one of the non-`client_admin` scopes and NOT include `listed` in the list of `profile_visibility_options`. If the Client has set their `profile_visibility` to `autolist`, when the Server approves at least one non-`client_admin` scope for production use, the Server MUST automatically switch the Client's `profile_visibility` setting from `autolist` to `listed` and remove `autolist` from the list of `profile_visibility_options`. The behavior of this option is the same as the `unlisted` Profile Visibility option, with the exception that the Server MUST prominently display a banner message in the rendered profile to users that the Client has not yet been approved for production use.
* `unlisted` - The Client profile will NOT be listed in the Server's public directory, but the `profile_uri` will be publicly visible when requested by a user directly and the response to the `profile_uri` MUST include a `X-robots-tag` response header with `noindex` in the header values or a `<meta name="robots" ...>` HTML head tag with `noindex` included in the `content` attribute.
* `disabled` - The Server MUST return a 404 Not Found response code for requests made to the Client's `profile_uri` and MUST NOT list the Client in the public directory.

### 6.3. Button Styles <a id="button-styles" href="#button-styles" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

### 6.4. Modifying Client Settings <a id="clients-settings-modify" href="#clients-settings-modify" class="permalink">🔗</a>

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

<span style="background-color:yellow">TODO: Security considerations on public directory descriptions, buttons, etc.</span>

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

