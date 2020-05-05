---
title: Business description
permalink: /business-description/
layout: default
---
## What is it?

Digital Signature Gateway service (Riigi allkirjastamisteenus) is a service for creating and signing ASIC-E and BDOC containers in a HASHCODE form. 
The service supports signing with Estonian ID-Card/e-token and with Estonian Mobile-ID. 

The service also supports validation of ASIC-E, BDOC and DDOC containers in hashcode form. 

If your e-service is currently using DigiDocService for container creation, take a look on DigiDocService comparision below to see what are the specific differences in business flow.

## What are the benefits?

Integration with Digital Signature Gateway service enables to have container and signature creation from single service with single contract. It do not require extra contract and integration for Mobile-ID or trust services
like Time Stamps and OCSP-s.

## Whom is it for?

The service can be used by all public sector authorities who are listed by the Rahandusministeerium. For specifics see [here](https://www.ria.ee/et/riigi-infosusteem/eid/partnerile.html#allkirjastamisteenus)

## What does it cost?

Current price list can be found [here.](https://www.ria.ee/et/riigi-infosusteem/eid/partnerile.html#allkirjastamisteenus)

## How can I join?

Demo environment can be used by everybody, however production environment can be used only by public sector authorities specified [here.](#whom-is-it-for)

Application for joining must be sent to help@ria.ee. Application forms can be found [here](https://www.ria.ee/et/riigi-infosusteem/eid/partnerile.html#allkirjastamisteenus)

## Whats the difference between Digital Signature Gateway service and DigiDocService?

Riigi allkirjastamisteenus offers two different services for integration (JSON and SOAP). JSON based service is new signing service intended to replace DigiDocService.
SOAP service is intended for legacy systems for easier transition. Both of the services have differences in functionality compared to DigiDocService.

### Differences in functionality

| **Functionality** | **DigiDocService** | **Digital Signature Gateway service** |  **Comment** | 
|-------------------|--------------------|----------|--------------|
| Container creation | Yes | Yes | Creating new containers |
| Adding signatures | Yes | Yes | Adding signatures to signed containers |
| Support for BDOC and ASICE containers | Yes | Yes | Supports the hashcode form |
| Support for DDOC container | Yes | **No** | Not possible to use |
| Container hashcode form | Yes | Yes | Same hashcode format |
| Signing with external device | Yes | Yes | ID card, e-seal, ... (certificate must be in Estonian TSL) |
| Signing with Mobile-ID | Yes | Yes | Riigi allkirjastamisteenus supports only Estonian Mobile-ID |
| Authentication with Mobile-ID | Yes | **No** | Riigi allkirjastamisteenus is purely signing/container service, use TARA for authentication |
| Verification of certificate validity | Yes | **No** | Not possible to validate |
| Signature validation | Yes | Yes | Riigi allkirjastamisteenus uses Valideerimisteenus (SIVA) for validation |

### Differences in protocol

| **General differences** | **DigiDocService** | **Digital Signature Gateway service SOAP**  | **Digital Signature Gateway service JSON** |  **Comment** | 
|-------------------------|--------------------|--------------------------------------|-------------------------------------|--------------|
| Protocol | SOAP/XML | SOAP/XML | REST/JSON | JSON API is described in WADL |
| Hashcode container format | DigiDocService | DigiDocService | DigiDocService | Same hahscode container format is used |
| Access to service | IP based | IP based | HMAC based authorization | JSON service each request must be signed by e-service |

## Additional questions?

Feel free to write help@ria.ee for any questions about Riigi allkirjastamisteenus.
