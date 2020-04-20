---
title: Business description
permalink: /business-description/
layout: default
---
# What is it?

Riigi allkirjastamisteenus is a service for creating and signing ASIC-E and BDOC containers in a [HASHCODE form](../technical-description#hashcode-container-form). 
The service supports signing with Estonian ID-Card/e-token and with Estonian Mobile-ID. Certificates used for signing must be present in [Estonian Trust List](https://sr.riik.ee/tsl/estonian-tsl.xml). 

The service also supports validation of ASIC-E, BDOC and DDOC containers in hashcode form. 

If your e-service is currently using DigiDocService for container creation, take a look on [DigiDocService comparision](#differences-between-riigi-allkirjastamisteenus-and-digidocservice) to see what are the specific differences in business flow.

# Whom is it for?

The service can be used by all public sector authorities who are listed by the Rahandusministeerium. The list can be found [here (XLSX)](https://www.rahandusministeerium.ee/sites/default/files/riigihaldus/avaliku_sektori_asutused_asutuse_liikide_loikes.xlsx)

# What does it cost?

Current price list can be found [here.](https://www.ria.ee/sites/default/files/content-editors/EID/lisa5_hinnakiri.pdf)

# How can I join?

Demo environment can be used by everybody, however production environment can be used only by public sector authorities specified [here.](#whom-is-it-for)

Steps required to join can be found [here.](../integration-process/)

# Differences between Riigi allkirjastamisteenus and DigiDocService

## Differences in functionality

| **Functionality** | **DigiDocService** | **Riigi allkirjastamisteenus** |  **Comment** | 
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



## Differences in protocol

### General differences

| **General differences** | **DigiDocService** | **Riigi allkirjastamisteenus** |  **Comment** | 
|-------------------------|--------------------|----------|--------------|
| Protocol | SOAP/XML | REST/JSON | API is described in WADL |
| Hashcode container format | DigiDocService | DigiDocService | Same hahscode container format is used |
| Access to service | IP based | HMAC based authorization | Each request must be signed by e-service |

### Method differences 

| **DigiDocService** | **Riigi allkirjastamisteenus** | **Comment** | 
|--------------------|----------|-------------|
| StartSession | - | "Session" is started in Riigi allkirjastamisteenus with upload or creation of container |
| CloseSession | DELETE: /hashcodecontainers/ {containerId} | |
| CreateSignedDoc | POST: /hashcodecontainers | |
| AddDataFile | POST: /hashcodecontainers/ {containerId}/datafiles | | 
| MobileSign | POST: /hashcodecontainers/ {containerId}/mobileidsigning | |
| GetStatusInfo | GET: /hashcodecontainers/ {containerId}/mobileidsigning/ {generatedSignatureId}/status | |
| GetSignedDocInfo | GET: /hashcodecontainers/ {containerId}/validationreport | |
| GetSignedDoc | GET: /hashcodecontainers/ {containerId} | |
| GetDataFile | GET: /hashcodecontainers/ {containerId}/datafiles | Retriever hashcode representation of data files |
| RemoveDataFile | DELETE: /hashcodecontainers/ {containerId}/datafiles/{fileName} | |
| RemoveSignature | - | Not supported in Riigi allkirjastamisteenus |
| GetSignersCertificate | - | Available through GET: /hashcodecontainers/ {containerId}/signatures/{signatureId} method |
| GetNotarysCertificate | - | Available through GET: /hashcodecontainers/ {containerId}/signatures/{signatureId} method |
| GetNotary | - | Not possible |
| GetVersion | - | Not possible |
| PrepareSignature | POST: /hashcodecontainers/ {containerId} /remotesigning | |
| FinalizeSignature | PUT: /hashcodecontainers/ {containerId} /remotesigning/{generatedSignatureId} | |
| MobileCreateSignature | - | Not possible |
| GetMobileCreateSignatureStatus | - | Not possible |
| GetMobileCertificate | - | Not possible |
| MobileSignHash | - | Not possible |
| GetMobileSignHashStatusRequest  | - | Not possible |
| - | POST: /upload/hashcodecontainers | In DigiDocService this is done with StartSession |
| - | GET: /hashcodecontainers/ {containerId}/signatures | Retrieves signatures list |
| - | GET: /hashcodecontainers/ {containerId} /signatures/{signatureId} | Retrieves information about signature, including used certificates |
| - | POST: /hashcodecontainers/ validationreport | Validates signatures without creating a session |

## Differences in flows

### Create container and sign with MID 

| **DigiDocService** | **Riigi allkirjastamisteenus** |  **Comment** | 
|----------|--------------------|--------------|
| [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID_DDS.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID_DDS.png) | [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID.png) | The flows have identical steps |

### Upload container and sign with ID card

| **DigiDocService** | **Riigi allkirjastamisteenus** |  **Comment** | 
|----------|--------------------|--------------|
| [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally_DDS.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally_DDS.png) | [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally.png) | The flows have identical steps |

## Validate signature 

| **DigiDocService** | **Riigi allkirjastamisteenus** |  **Comment** | 
|----------|--------------------|--------------|
| [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation_DDS.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation_DDS.png) | [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation.png) | Flows are different |



