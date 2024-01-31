---
layout: base
nav: specs
title: CDSC-WG1 - 02 Client Registration - Overview
meta_description: Linux Foundation Energy - Carbon Data Specification Consortium (CDSC) - Customer DataWorking Group (WG1) - Specifications - cdsc-wg1-02 - Client Registration - Overview
---
[Home]({{ "/" | relative_url }}) / [Specifications]({{ "/specs" | relative_url }}) / [`cdsc-wg1-02` - Client Registration]({{ "/specs/cdsc-wg1-02" | relative_url }}) / Overview

# Client Registration - Overview

This is a summary overview of [`cdsc-wg1-02`]({{ "/specs/cdsc-wg1-02" | relative_url }}), which
describes how companies and technical users ("Clients") can register, onboard, and perform
authenticated actions with utilities and other centralized entities ("Servers"). This specification
builds upon [`cdsc-wg1-01`]({{ "/specs/cdsc-wg1-01" | relative_url }}), which allows Clients to
discover a Server's metadata and capabilities. This specification adds a capability to the Server's
metadata that specifies to a Client how to register with that Server.

For example, this specification describes how an energy efficiency app can register with a utility
so that the app can request access from a utility customer's to see their last year of energy usage.

## Background <a id="background" href="#background" class="permalink">🔗</a>

Utilities and other central entities offer many different ways of providing authenticated access
to information, including how to obtain customer authorization when customers need to authorize
access to their utility data. For [use case]({{ "/use-cases" | relative_url }}) companies needing to
obtain customer data, an efficient means of registering, onboarding, and configuring authenticated
access with these utilities and other central entities is needed.

## OAuth Capability <a id="oauth-capability" href="#oauth-capability" class="permalink">🔗</a>

```
==Request==
GET /.well-known/carbon-data-spec.json HTTP/1.1
Host: demoutility.com

==Response==
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8

{
    "cds_metadata_version": "v1",
    ...

    "capabilities": [
        "oauth",
        ...
    ],
    ...

    "oauth_metadata": "https://demoutility.com/.well-known/oauth-authorization-server"
}
```

## OAuth Metadata Endpoint <a id="oauth-metadata" href="#oauth-metadata" class="permalink">🔗</a>

```
==Request==
GET /.well-known/oauth-authorization-server HTTP/1.1
Host: demoutility.com

==Response==
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8

{
    # OAuth Metadata

    ## OAuth Metadata - General information
    "issuer": "https://demoutility.com",
    "service_documentation": "https://demoutility.com/docs/oauth",
    "op_policy_uri": "https://demoutility.com/legal/oauth-policy",
    "op_tos_uri": "https://demoutility.com/legal/oauth-terms",

    ## OAuth Metadata - Functional endpoints
    "registration_endpoint": "https://demoutility.com/oauth/register",
    "authorization_endpoint": "https://demoutility.com/oauth/authorize",
    "token_endpoint": "https://demoutility.com/oauth/token",
    "revocation_endpoint": "https://demoutility.com/oauth/token/revoke",
    "introspection_endpoint": "https://demoutility.com/oauth/token/info",
    "pushed_authorization_request_endpoint": "https://demoutility.com/oauth/par",
    "code_challenge_methods_supported": [
        "S256"
    ],

    ## OAuth Metadata - Scopes available
    "scopes_supported": [
        "client_admin",
        "usage",
        ...
    ],
    "authorization_details_types_supported": [
        "client_admin",
        "usage",
        ... (same as "scopes_supported")
    ]


    # CDS Extension
    "cds_oauth_version": "v1",

    # CDS Extension - Required human-method alternative links
    "cds_human_registration": "https://demoutility.com/clients/register",
    "cds_client_directory": "https://demoutility.com/clients/directory",
    "cds_test_accounts": "https://demoutility.com/docs/oauth/test-accounts",

    # CDS Extension - Required API endpoints
    "cds_clients_api": "https://demoutility.com/api/clients",
    "cds_client_settings_api": "https://demoutility.com/api/client-settings",
    "cds_client_updates_api": "https://demoutility.com/api/client-updates",
    "cds_scope_credentials_api": "https://demoutility.com/api/scope-credentials",
    "cds_grants_api": "https://demoutility.com/api/grants",

    # CDS Extension - scope descriptions and requirements
    "cds_scope_descriptions": {

        "client_admin": {
            ## Information about the scope
            "name": "Client Administrator Access",
            "description": "Allows ability to view and manage the requesting client's registration.",
            "documentation": "https://demoutility.com/docs/oauth/scopes#client_admin",

            ## Extra fields required for registration
            "registration_requirements": [],

            ## OAuth access configuration for this scope
            "response_types_supported": [],
            "grant_types_supported": ["client_credentials"],
            "token_endpoint_auth_methods_supported": ["client_secret_basic"],
            "code_challenge_methods_supported": [],

            ## Rich Authorization Request 
            "authorization_details_additional_fields": [],
        },

        "bills": {
            ## Information about the scope
            "name": "Customer bill data access",
            "description": "Allows access to the authorizing customer's utility bill data.",
            "documentation": "https://demoutility.com/docs/customer-data-access/bills",

            ## Extra fields and steps required for registration
            "registration_requirements": ["sou", "review_step1", "setup_fee1"],
            "registration_optional": ["tos_url", "dir_autolist"],

            ## OAuth access configuration for this scope
            "response_types_supported": ["code"],
            "grant_types_supported": ["authorization_code", "refresh_token"],
            "token_endpoint_auth_methods_supported": ["none", "client_secret_basic"],
            "code_challenge_methods_supported": ["S256"],

            ## Rich Authorization Request 
            "authorization_details_fields": [
                {
                    "name": "historical_start",
                    "type": "relative_or_absolute_date",
                    "default": "3y",
                    "description": #############TODO
                },
                {
                    "name": "ongoing_end",
                    "type": "relative_or_absolute_date",
                    "default": "3y",
                    "description": #############TODO
                },
            ],
        },
        ...
    },

    # CDS Extension - Additional registration fields
    "cds_registration_fields": {
        "sou": {
            "type": "registration_field",
            "field_name": "cds_initial_scope_of_use",
            "description": "Describe how you will use customer data that is authorized to you.",
            "documentation": "https://demoutility.com/docs/oauth/registration#sou",
            "type": "string_or_null",
            "max_length": 500,
            "default": null,
        },
        "tos_url": {
            "type": "registration_field",
            "field_name": "cds_initial_scope_of_use_tos_url",
            "description": "Optionally, provide a link to your terms for your scope of use.",
            "documentation": "https://demoutility.com/docs/oauth/registration#tos_url",
            "type": "url_or_null",
            "default": null,
        },
        "dir_autolist": {
            "type": "registration_field",
            "field_name": "cds_autolist_in_directory",
            "description": "Do you want to be automatically listed in the public client directory when registered and approved?",
            "documentation": "https://demoutility.com/docs/oauth/registration#dir-autolist",
            "type": "boolean",
            "default": true,
        },
        "review_step1": {
            "type": "manual_review",
            "name": "Utility Review",
            "description": "Demo Utility will manually review your client registration before enabling live access.",
            "documentation": "https://demoutility.com/docs/oauth/registration#review-process",
        },
        "setup_fee1": {
            "type": "payment_required",
            "name": "Registration Fee",
            "amount": 2000,
            "currency": "USD",
            "description": "Demo Utility requires a setup fee payment of $20 before enabling live access.",
            "documentation": "https://demoutility.com/docs/oauth/registration#review-process",
        },
        ...
    ],
}







```

## Dynamic Client Registration <a id="client-registration" href="#client-registration" class="permalink">🔗</a>

```
==Request==
POST /.well-known/carbon-data-spec.json HTTP/1.1
Content-Type: application/json
Host: demoutility.com

{
    # Normal OAuth registration fields
    "client_name": "Example EV Company",
    "client_uri": "https://example.com",
    "logo_uri": "https://example.com/logo.png",
    "tos_uri": "https://example.com/terms",
    "policy_uri": "https://example.com/privacy",
    "contacts": [
        "mailto:aaa@bbb.com",
        "tel:+15554443333",
    ],
    "scope": "client_admin accounts ..."
    "redirect_uris": [
        "https://client.example.org/callback",
        "https://client.example.org/callback2",
    ],

    # CDS Extension - additional registration fields
    "cds_initial_scope_of_use": "For testing purposes only.",
    "cds_autolist_in_directory": false,
}


==Response==
HTTP/1.1 201 Created
Content-Type: application/json;charset=UTF-8

{
    ...client metadata entry (see client metadata endpoint for format)
}
```



## Client Endpoint <a id="client-endpoint" href="#client-endpoint" class="permalink">🔗</a>

```
==Request==
GET /api/clients HTTP/1.1
Host: demoutility.com
Authorization: Bearer <access_token>

{
    "results": [
        {
            # Normal OAuth client metadata
            "client_id": "aaaaaaaaaa",
            "client_id_issued_at": 2893256800,

            "client_name": "Example EV Company",
            "client_uri": "https://example.com",
            "logo_uri": "https://example.com/logo.png",
            "tos_uri": "https://example.com/terms",
            "policy_uri": "https://example.com/privacy",
            "contacts": [
                "mailto:aaa@bbb.com",
                "tel:+15554443333",
            ],

            # CDS Extenison - info for client
            "cds_server_metadata": "https://server.com/oauth/clients/aaaaaaaaaa-01/carbon-data-spec.json",

            # CDS Extension - extended settings
            "cds_settings_api": "https://server.com/oauth/clients/aaaaaaaaaa-01/settings",
            "cds_settings": {
                "default_scope": "accounts ...",
                "scopes_of_use": [
                    {
                        "id": "default",
                        "description": "For testing purposes only.",
                        "tos_uri": null,
                    },
                ],
                "public_directory": {
                    "profile_uri": "https://server.com/directory/my-client",
                    "profile_description": "Some description here...",
                    "directory_slug": "my-client",
                    "visibility": "autolist",
                    "visibility_options": [
                        "autolist",
                        "unlisted",
                        "disabled",
                    ],
                    "button_uri": "https://example.com/share/data",
                    "button_style": "share_data",
                    "button_style_options": [
                        "share_data",
                        "connect",
                        "authorize",
                    ],
                    "contact_name": "ABC Customer Support",
                    "contact_email": "support@example.com",
                    "contact_phone": "+15552223333",
                },
            },

            # CDS Extensions - updates and notifications
            "cds_updates_api": "https://server.com/oauth/clients/aaaaaaaaaa-01/updates",
            "cds_updates_latest": "2022-01-01T00:00:00Z",
            "cds_updates_outstanding": [
                {
                    "uri": "https://demoutility.com/clients/aaaaaaaaaa-01/updates/444444444444",
                    "created_at": "2022-01-01T00:00:00Z",
                    "modified_at": "2022-01-01T00:00:00Z",
                    "status": "pending",
                    "updates_requested": [
                        {
                            "name": "client_name",
                            "previous_value": "My Example Client",
                            "new_value": "My New Name",
                        },
                    ],
                },
            ],
            "cds_updates_outstanding_has_more": false,

            # CDS Extensions - client scope credentials
            "cds_scope_credentials_api": "https://server.com/oauth/clients/aaaaaaaaaa-01/scope-credentials",
            "cds_scope_credentials": [
                {
                    "id": "555555555",
                    "scope": "client_admin",
                    "client_secret": "ccccccccccccccccccccccccccc",
                    "client_secret_expires_at": 2893276800,
                    "status": "live",
                    "status_options": [
                        "live",
                    ],
                    "response_types": [],
                    "grant_types": [
                        "client_credentials",
                    ],
                    "token_endpoint_auth_method": "client_secret_basic",
                },
                {
                    "id": "6666666666",
                    "scope": "accounts ...",
                    "client_secret": "dddddddddddddddddddddd",
                    "client_secret_expires_at": 2893276800,
                    "status": "sandbox",
                    "status_options": [
                        "sandbox",
                        "disabled",
                    ],
                    "response_types": [
                        "code",
                    ],
                    "redirect_uris": [
                        "https://client.example.org/callback",
                        "https://client.example.org/callback2",
                    ],
                    "grant_types": [
                        "authorization_code",
                        "refresh_token",
                    ],
                    "token_endpoint_auth_method": "client_secret_basic",
                },
                {
                    "id": "7777777777",
                    "scope": "accounts ...",
                    "status": "sandbox",
                    "status_options": [
                        "sandbox",
                        "disabled",
                    ],
                    "response_types": [
                        "code",
                    ],
                    "redirect_uris": [
                        "https://client.example.org/callback",
                        "https://client.example.org/callback2",
                    ],
                    "grant_types": [
                        "authorization_code",
                        "refresh_token",
                    ],
                    "token_endpoint_auth_method": "none",
                },
            ],
            "cds_scope_credentials_has_more": false,
        },
    ],
    "next": null,
    "previous": null
}
```

---

# Other Drafts <a id="other-drafts" href="#other-drafts" class="permalink">🔗</a>

* Maintainer's draft: [[Website](https://daniel-utilityapi.github.io/Customer-Data/specs/cdsc-wg1-02/overview)] [[Code](https://github.com/daniel-utilityapi/Customer-Data/blob/main/website/specs/cdsc-wg1-02/overview.md)]
