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
    "issuer": "https://demoutility.com",

    ## OAuth Metadata - general info and docs
    "service_documentation": "https://demoutility.com/docs/oauth",
    "op_policy_uri": "https://demoutility.com/legal/oauth-policy",
    "op_tos_uri": "https://demoutility.com/legal/oauth-terms",

    ## OAuth Metadata - endpoints
    "registration_endpoint": "https://demoutility.com/oauth/register",
    "authorization_endpoint": "https://demoutility.com/oauth/authorize",
    "token_endpoint": "https://demoutility.com/oauth/token",
    "revocation_endpoint": "https://demoutility.com/oauth/token/revoke",
    "introspection_endpoint": "https://demoutility.com/oauth/token/info",

    ## OAuth Metadata - functionality supported
    "scopes_supported": [
        "client_admin",
        "accounts",
        "services",
        "programs",
        "bills",
        "usage",
        "aggregate_usage"
    ],
    "response_types_supported": [
        "code",
    ],
    "token_endpoint_auth_methods_supported": [
        "none",
        "client_secret_basic",
    ],
    "grant_types_supported": [
        "authorization_code",
        "client_credentials",
        "refresh_token",
    ],
    "code_challenge_methods_supported": [
        "S256",
    ],


    # CDS Extension
    "cds_oauth_version": "v1",
    "cds_clients_api": "https://demoutility.com/api/clients",

    # CDS Extension - human-method alternative
    "cds_human_registration": "https://demoutility.com/clients/register",
    "cds_client_directory": "https://demoutility.com/clients/directory",

    # CDS Extension - additional registration fields
    "cds_registration_extras": [
        {
            "name": "cds_initial_scope_of_use",
            "description": "Describe how you will use customer data that is authorized to you."
            "type": "string",
            "default": null,
        },
        {
            "name": "cds_initial_scope_of_use_tos_uri",
            "description": "Optionally, provide a link to your terms for your scope of use."
            "type": "url",
            "default": null,
        },
        {
            "name": "cds_autolist_in_directory",
            "description": "Do you want to be automatically listed in the public client directory when registered and approved?"
            "type": "boolean",
            "default": true,
        },
        ...
    ],

    # CDS Extension - scope descriptions and requirements
    "cds_scope_descriptions": {
        "client_admin": {
            "description": "Allows ability to view and manage the requesting client's registration.",
            "grant_types": [
                "client_credentials",
            ],
            "token_endpoint_auth_methods": [
                "client_secret_basic",
            ],
            "requirements": [],
            "scope_modifiers": [],
        },
        "accounts": {
            "description": "Allows access to a customers"
            "grant_types": [
                "authorization_code",
                "refresh_token"
            ],
            "token_endpoint_auth_methods": [
                "none",
                "client_secret_basic",
            ],
            "requirements": [
                {
                    "id": "sou"
                    "type": "registration_field",
                    "field_name": "cds_initial_scope_of_use",
                    "name": "Scope of Use",
                    "description": "You must describe how you will use this data when authorized access by customers.",
                },
                {
                    "id": "review_step1"
                    "type": "manual_review",
                    "name": "Utility Review",
                    "description": "Demo Utility will manually review your client registration before allowing use of this scope.",
                },
            ],
            "scope_modifiers": {
                "_number": {
                    "name": "Account Number",
                    "description": "Allow access to a customer's account number for the authorized accounts.",
                    "requirements": [],
                },
            },
        },
        ...
    },

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
            "client_secret": "ccccccccccccccccccccccccccc",
            "client_secret_expires_at": 2893276800,
            "scope": "client_admin",  # NOTE: see cds_scope_credentials for additional scopes

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
                    "token_endpoint_auth_methods_supported": [
                        "client_secret_basic",
                    ],
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
                    "grant_types": [
                        "authorization_code",
                        "refresh_token",
                    ],
                    "token_endpoint_auth_methods_supported": [
                        "none",
                        "client_secret_basic",
                    ],
                },
            ],
            "cds_scope_credentials_has_more": false,
        },
    ],
    "next": null,
    "previous": null
}
```

### Client Settings Management <a id="client-settings-management" href="#client-settings-management" class="permalink">🔗</a>

TODO

### Client Updates <a id="client-updates" href="#client-updates" class="permalink">🔗</a>

TODO

### Client Scope Credentials Management <a id="client-secrets-management" href="#client-secrets-management" class="permalink">🔗</a>

TODO

---

# Other Drafts <a id="other-drafts" href="#other-drafts" class="permalink">🔗</a>

* Maintainer's draft: [[Website](https://daniel-utilityapi.github.io/Customer-Data/specs/cdsc-wg1-02/overview)] [[Code](https://github.com/daniel-utilityapi/Customer-Data/blob/main/website/specs/cdsc-wg1-02/overview.md)]
