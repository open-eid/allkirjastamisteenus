---
title: Business description
permalink: /business-description/
layout: default
---
## What is it?

Digital Signature Gateway service (Riigi allkirjastamisteenus) is a service for creating and signing ASIC-E and BDOC containers in a HASHCODE form. 

The service supports signature creation with Estonian ID-card/e-token (signing with ID-card/e-token must be still implemented in e-service) and with Estonian Mobile-ID. 

The service also supports validation of ASIC-E, BDOC and DDOC containers in hashcode form. 

If your e-service is currently using DigiDocService for container creation, take a look on DigiDocService comparision below to see what are the specific differences in business flow.

## What are the benefits?

Digital Signature Gateway service offers unified API for container and signature creation. No need for extra integration with Mobile-ID or trust services. 
API is platform independent and can be used in any e-service capable of handling JSON/REST API.

Only one contract is needed to get access container and signature creation. No need for handling contracts and billing with different parties. 

Digital Signature Gateway service is kept up to date with security patches and compliant with signature standards. E-service maintenance is limited to the updates for API integration. 

## Whom is it for?

The service can be used by all public sector authorities who are listed by the Rahandusministeerium. For specifics see [here](https://www.ria.ee/et/riigi-infosusteem/eid/partnerile.html#allkirjastamisteenus)

## What does it cost?

Current price list can be found [here.](https://www.ria.ee/et/riigi-infosusteem/eid/partnerile.html#allkirjastamisteenus)

## How can I join?

Demo environment can be used by everybody, however production environment can be used only by public sector authorities specified [here.](#whom-is-it-for)

Application for joining must be sent to help@ria.ee. Application forms can be found [here](https://www.ria.ee/et/riigi-infosusteem/eid/partnerile.html#allkirjastamisteenus)

## Whats the difference between Digital Signature Gateway service and DigiDocService?

Digital Signature Gateway service offers two different services for integration (JSON and SOAP). JSON based service is new signing service intended to replace DigiDocService.
SOAP service is intended for legacy systems for easier transition. Both of the services have differences in functionality compared to DigiDocService.

### Differences in functionality

| **Functionality** | **DigiDocService** | **Digital Signature Gateway service** |  **Comment** | 
|-------------------|--------------------|----------|--------------|
| Container creation | Yes | Yes | Creating new containers |
| Adding signatures | Yes | Yes | Adding signatures to containers |
| Support for BDOC and ASICE containers | Yes | Yes | Supports the hashcode form |
| Support for DDOC container | Yes | **No** | Not possible to use |
| Container hashcode form | Yes | Yes | Same hashcode format |
| Signing with external device | Yes | Yes | ID card, e-seal, ... (certificate must be in Estonian TSL) |
| Signing with Mobile-ID | Yes | Yes | Digital Signature Gateway service supports only Estonian Mobile-ID |
| Authentication with Mobile-ID | Yes | **No** | Digital Signature Gateway service is purely signing/container service, use TARA for authentication |
| Verification of certificate validity | Yes | **No** | Not possible to validate |
| Signature validation | Yes | Yes | Digital Signature Gateway service uses Valideerimisteenus (SIVA) for validation |

### Differences in protocol

| **General differences** | **DigiDocService** | **Digital Signature Gateway service SOAP**  | **Digital Signature Gateway service JSON** |  **Comment** | 
|-------------------------|--------------------|--------------------------------------|-------------------------------------|--------------|
| Protocol | SOAP/XML | SOAP/XML | REST/JSON | JSON API is described in WADL |
| Hashcode container format | DigiDocService | DigiDocService | DigiDocService | Same hahscode container format is used |
| Access to service | IP based | IP based | HMAC based authorization | JSON service each request must be signed by e-service |

## Additional questions?

Feel free to write help@ria.ee for any questions about Digital Signature Gateway service.
