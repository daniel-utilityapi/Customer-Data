# CDSC-WG1-03 - Customer Data

## Abstract <a id="abstract" href="#abstract" class="permalink">🔗</a>

This specification defines how utilities and other central entities ("Servers") may allow third parties ("Clients") to access utility account holder data ("Customer Data"), including for uses cases that require account holders to authorize the access. This specification extends CDSC-WG1-02 ([Client Registration](/specs/cdsc-wg1-02)) to add OAuth authorization scopes for Customer Data, as well as defining the APIs used to access Customer Data when authorization is granted.

## Status <a id="status" href="#status" class="permalink">🔗</a>

<b style="color:red">WARNING: This specification is a DRAFT and has not achieved consensus by the working group.</b>

## Copyright <a id="copyright" href="#copyright" class="permalink">🔗</a>

<span style="background-color:yellow">TODO</span>

## Table of Contents <a id="table-of-contents" href="#table-of-contents" class="permalink">🔗</a>

* [1. Introduction](#introduction)  
* [2. Terminology](#terminology)  
* [3. Authorization](#authorization)  
    * [3.1. Authorization Scopes](#authorization-scopes)  
    * [3.2. Defining Requirements for Scope Registration](#authorization-requirements)  
    * [3.3. Client Metadata](#client-metadata)  
* [4. Account API](#account-api)  
    * [4.1. Account Object Format](#account-format)  
    * [4.2. Listing Accounts](#accounts-list)  
* [5. ServiceContract API](#servicecontract-api)  
    * [5.1. ServiceContract Object Format](#servicecontract-format)  
    * [5.2. Listing ServiceContracts](#servicecontract-list)  
* [6. ServicePoint API](#servicepoint-api)  
    * [6.1. ServicePoint Object Format](#servicepoint-format)  
    * [6.2. Listing ServicePoints](#servicepoint-list)  
* [7. BillStatement API](#billstatement-api)  
    * [7.1. BillStatement Object Format](#billstatement-format)  
    * [7.2. Listing BillStatements](#billstatement-list)  
* [8. BillSection API](#billsection-api)  
    * [8.1. BillSection Object Format](#billsection-format)  
    * [8.2. Listing BillSections](#billsection-list)  
* [9. Aggregation API](#aggregation-api)  
    * [9.1. Aggregation Object Format](#aggregation-format)  
    * [9.2. Listing Aggregations](#aggregation-list)  
* [10. Usage API](#usage-api)  
    * [10.1. Usage Object Format](#usage-format)  
    * [10.2. Listing Usage](#usage-list) 
* [11. Extensions](#extensions)  
* [12. Examples](#examples)  
* [13. Security Considerations](#security)  
    * [13.1. Rate Limiting](#rate-limiting)  
* [14. References](#references)  
* [15. Acknowledgments](#acknowledgments)  
* [16. Authors' Addresses](#authors-addresses)  
