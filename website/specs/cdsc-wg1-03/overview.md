---
layout: base
nav: specs
title: CDSC-WG1 - 03 Customer Data - Overview
meta_description: Linux Foundation Energy - Carbon Data Specification Consortium (CDSC) - Customer DataWorking Group (WG1) - Specifications - cdsc-wg1-03 - Customer Data - Overview
---
[Home]({{ "/" | relative_url }}) / [Specifications]({{ "/specs" | relative_url }}) / [`cdsc-wg1-03` - Customer Data]({{ "/specs/cdsc-wg1-03" | relative_url }}) / Overview

# Customer Data - Overview

This is a summary overview of [`cdsc-wg1-03`]({{ "/specs/cdsc-wg1-03" | relative_url }}), which
describes how companies and technical users ("Clients") can request access to customer data from
utilities and other centralized entities ("Servers"). This specification builds upon
[`cdsc-wg1-02`]({{ "/specs/cdsc-wg1-02" | relative_url }}), which allows Clients to register with
a Server and be granted access to request various scopes of data from that Server using the OAuth
protocol.

For example, this specification describes how an energy efficiency app can request access from a
utility customer to see their account number, rate plan, and last year of energy usage.

## Background <a id="background" href="#background" class="permalink">🔗</a>

TODO

## Customer Data Scopes <a id="scopes" href="#scopes" class="permalink">🔗</a>

```
### What fields
* `accounts`
* `accounts_number`
* `accounts_name`
* `services`
* `services_electric_only`
* `services_gas_only`
* `services_water_only`
* `services_tariff`
* `services_status`
* `services_address`
* `programs`
* `programs_eligibility=*`
* `programs_signup=*`
* `bills`
* `bills_service_sections`
* `bills_frontpage_accounting`
* `bills_pdf`
* `bills_amount_due`
* `usage`
* `usage_intervals`
* `usage_by_bill_cycle`
* `usage_by_month`
* `aggregate_usage`
* `aggregate_usage_building_monthly`

### UX hints
* `auth_hint_login=*`
* `auth_hint_lookup_id=*`
* `auth_hint_lookup_email=*`
* `auth_hint_lookup_phone_number=*`
* `auth_hint_lookup_service_address=*`
* `auth_hint_lookup_account_number=*`
* `auth_otp_call_preferred`
* `auth_otp_sms_preferred`
* `auth_otp_email_preferred`
* `auth_noedit`

### What accounts/services
* `select_all`
* `select_all_including_future`
* `select_by_account_numbers=*`
* `select_by_service_addresses=*`
* `select_by_service_types=*`
* `select_by_service_numbers=*`
* `select_by_meter_numbers=*`
* `select_by_building_numbers=*`
* `select_by_programs=*`
* `select_by_tariffs=*`
* `select_by_premise_numbers=*`
* `select_limit_to_preselected`

### Duration
* `historical_max`
* `historical_latest_bill_cycle`
* `historical_NNNy`
* `historical_NNNm`
* `historical_NNNd`
* `historical_YYYY-MM-DD`
* `ongoing_indefinite`
* `ongoing_NNNy`
* `ongoing_NNNm`
* `ongoing_NNNd`
* `ongoing_YYYY-MM-DD`

### Client Credentials
* `client_admin`
* `data_requests_all`
* `building_search`
* `customer_search`
* `cds_server_metadata`
```
### Scope Examples <a id="scope-examples" href="#scope-examples" class="permalink">🔗</a>

```
* last 12 months of bills and intervals for all services
    * `bills usage historical_1y select_all`

* current service list
    * `services select_all`

* ongoing access to service list
    * `services ongoing_1y select_all_including_future`

* property manager going forward
    * `accounts services programs bills bills_pdf intervals select_all_including_future ongoing_indefinite`

* see authorizations list (client_credentials)
    * `data_requests_all`

* whole-building benchmarking request (client_credentials)
    * `building_search`
    * `select_by_building_numbers=C111111 historical_2020-01-01 aggregate_building_monthly`
```

---

# Other Drafts <a id="other-drafts" href="#other-drafts" class="permalink">🔗</a>

* Maintainer's draft: [[Website](https://daniel-utilityapi.github.io/Customer-Data/specs/cdsc-wg1-03/overview)] [[Code](https://github.com/daniel-utilityapi/Customer-Data/blob/main/website/specs/cdsc-wg1-03/overview.md)]
