---
title: DigiDocService comparision
permalink: /digidocservice-comparision/
layout: default
---

## Introduction

This document compares SiGa service and DigiDocService (both container and Mobile-ID features). This should give an overview of possible changes needed to migrate.

## Differences in functionality

| **Functionality** | **DigiDocService** | **SiGa** |  **Comment** | 
|-------------------|--------------------|----------|--------------|
| Container creation | Yes | Yes | Creating new containers |
| Adding signatures | Yes | Yes | Adding signatures to signed containers |
| Support for BDOC and ASICE containers | Yes | Yes | Supports the hashcode form |
| Support for DDOC container | Yes | **No** | Not possible to use |
| Container hashcode form | Yes | Yes | Same hashcode format |
| Signing with external device | Yes | Yes | ID card, e-seal, ... (certificate must be in Estonian TSL) |
| Signing with Mobile-ID | Yes | Yes | SiGa supports only Estonian Mobile-ID |
| Authentication with Mobile-ID | Yes | **No** | SiGa is purely signing/container service, use TARA for authentication |
| Verification of certificate validity | Yes | **No** | Not possible to validate |
| Signature validation | Yes | Yes | SiGA uses Valideerimisteenus (SIVA) for validation |



## Differences in protocol

### General differences

| **General differences** | **DigiDocService** | **SiGa** |  **Comment** | 
|-------------------------|--------------------|----------|--------------|
| Protocol | SOAP/XML | REST/JSON | API is described in WADL |
| Hashcode container format | DigiDocService | DigiDocService | Same hahscode container format is used |
| Access to service | IP based | HMAC based authorization | Each request must be signed by e-service |

### Method differences 

| **DigiDocService** | **SiGa** | **Comment** | 
|--------------------|----------|-------------|
| StartSession | - | "Session" is started in SiGa with upload or creation of container |
| CloseSession | DELETE: /hashcodecontainers/ {containerId} | |
| CreateSignedDoc | POST: /hashcodecontainers | |
| AddDataFile | POST: /hashcodecontainers/ {containerId}/datafiles | | 
| MobileSign | POST: /hashcodecontainers/ {containerId}/mobileidsigning | |
| GetStatusInfo | GET: /hashcodecontainers/ {containerId}/mobileidsigning/ {generatedSignatureId}/status | |
| GetSignedDocInfo | GET: /hashcodecontainers/ {containerId}/validationreport | |
| GetSignedDoc | GET: /hashcodecontainers/ {containerId} | |
| GetDataFile | GET: /hashcodecontainers/ {containerId}/datafiles | Retriever hashcode representation of data files |
| RemoveDataFile | DELETE: /hashcodecontainers/ {containerId}/datafiles/{fileName} | |
| RemoveSignature | - | Not supported in SiGa |
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

| **DigiDocService** | **SiGa** |  **Comment** | 
|----------|--------------------|--------------|
| [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID_DDS.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID_DDS.png) | [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID.png) | The flows have identical steps |

### Upload container and sign with ID card

| **DigiDocService** | **SiGa** |  **Comment** | 
|----------|--------------------|--------------|
| [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally_DDS.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally_DDS.png) | [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally.png) | The flows have identical steps |

## Validate signature 

| **DigiDocService** | **SiGa** |  **Comment** | 
|----------|--------------------|--------------|
| [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation_DDS.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation_DDS.png) | [![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation.png)](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation.png) | Flows are different |




