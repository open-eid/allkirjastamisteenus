---
title: SOAP interface
permalink: /soap-technical-description/
layout: default
---
| Table of contents |
|-------------------|
|[Introduction](#introduction)|
|[Access to the service](#access-to-the-service)|
|[Differences in functionality](#differences-in-functionality)|
|[Hashcode Container form](#hashcode-container-form)|
|[Differences in protocol](#differences-in-protocol)|
|[Differences in error handling](#differences-in-error-handling)|

## Introduction

Digital Signature Gateway service (Riigi allkirjastamisteenus) offers a SOAP based interface for **legacy systems** currently using DigiDocService. It is **strongly recommended** to use JSON based api for integration even if you currently use DigiDocService.

SOAP interface is based on SK ID Solutions AS [DigiDocService API](../dds-api) however, it do not implement the full functionality.
Current e-service integration needs to be analyzed in regards of suitability for switching to Digital Signature Gateway service and some changes in integration may be required.

If required changes are substantial we suggest integrating JSON interface instead of SOAP interface.

For analyzing whether the Digital Signature Gateway service SOAP interface is suitable for your integration please revise the below chapters.

## Access to the service

Accessing the service is IP based, it is needed to register for usage. See [Information System Authority](https://www.ria.ee/et/riigi-infosusteem/eid/partnerile.html#allkirjastamisteenus) webpage for registration info.

## Differences in functionality

| **Functionality** | **DigiDocService** | **Digital Signature Gateway service** |  **Comment** | 
|-------------------|--------------------|----------|--------------|
| Container creation | Yes | Yes | Creating new containers |
| Adding signatures | Yes | Yes | Adding signatures to signed containers |
| Support for BDOC and ASICE containers | Yes | Yes | Digital Signature Gateway service supports the hashcode form. No datafiles support. |
| Support for DDOC container | Yes | **No** | Not possible to use |
| Container hashcode form | Yes | Yes | Same hashcode format |
| Signing with external device | Yes | Yes | ID card, e-seal, ... (certificate must be in Estonian TSL) |
| Signing with Mobile-ID | Yes | Yes | Digital Signature Gateway service supports only Estonian Mobile-ID |
| Authentication with Mobile-ID | Yes | **No** | Digital Signature Gateway service is purely signing/container service, use TARA for authentication |
| Verification of certificate validity | Yes | **No** | Not possible to validate |
| Signature validation | Yes | Yes | Digital Signature Gateway service uses Valideerimisteenus (SIVA) for validation |

## Hashcode container form

Digital Signature Gateway service supports only hashcode based data files manipulation. This means that signed data files are not leaving the integrating e-service premises giving protection to the content. 
In addition this enables to sign large data files as hashcode representation of the file is not tied to the size of real file.

The details how to convert containers to and from hashcode form can be found [here.](https://github.com/open-eid/SiGa/wiki/Hashcode-container-form)

## Differences in protocol

Digital Signature Gateway service only supports Digital Signature API. No methods for authentication are supported.

### Method differences 

| **Method** | **DigiDocService** | **Digital Signature Gateway service** | **Comment** | 
|--------------------|----------|-------------|
| StartSession | Yes | Yes | Difference in supported parameters. For details check [here.](#startsession) |
| CloseSession | Yes | Yes | No difference in parameters. |
| CreateSignedDoc | Yes | Yes | Difference in supported parameters. For details check [here.](#createsigneddoc) |
| AddDataFile | Yes |  Yes | Difference in supported parameters. For details check [here.](#adddatafile) | 
| MobileSign | Yes |  Yes | Difference in supported parameters. For details check [here.](#mobilesign) |
| GetStatusInfo | Yes |  Yes | Difference in supported parameters. For details check [here.](#getstatusinfo) |
| GetSignedDocInfo | Yes |  Yes | No difference in parameters. |
| GetSignedDoc | Yes | Yes | No difference in parameters. |
| GetDataFile | Yes | **No** | Not possible |
| RemoveDataFile | Yes | Yes | Difference in supported parameters. For details check [here.](#removedatafile) |
| RemoveSignature | Yes | **No** | Not possible |
| GetSignersCertificate | Yes | **No** | Not possible |
| GetNotarysCertificate | Yes | **No** | Not possible |
| GetNotary | Yes |  **No** | Not possible |
| GetVersion | Yes |  **No** | Not possible |
| PrepareSignature | Yes |  Yes | Difference in supported parameters. For details check [here.](#preparesignature) |
| FinalizeSignature | Yes |  Yes | Difference in supported parameters. For details check [here.](#finalizesignature) |
| MobileCreateSignature | Yes | **No** | Not possible |
| GetMobileCreateSignatureStatus | Yes | **No** | Not possible |
| GetMobileCertificate | Yes | **No** | Not possible |
| MobileSignHash | Yes | **No** | Not possible |
| GetMobileSignHashStatusRequest  | Yes | **No** | Not possible |

### Parameter differences

#### **StartSession**

| **Parameter** | **Comment** | 
|---------------|-------------|
| SigDocXML   | Not possible to use for DDOC.  |
| datafile    | Creation of DDOC not supported.  |

#### **CreateSignedDoc**

| **Parameter** | **Comment** | 
|---------------|-------------|
| Format | Only BDOC supported. |
| Version | Only 2.1 supported. |
| SigningProfile | LT (signatures with TimeStamp) is default profile. |

#### **AddDataFile**

| **Parameter** | **Comment** | 
|---------------|-------------|
| MimeType   | Not handled, will be set automatically.  |
| ContentType | Only HASHCODE supported.  |
| DigestType  | Only sha256 supported.  |
| Content | Not supported.  |

#### **MobileSign**

| **Parameter** | **Comment** | 
|---------------|-------------|
| SignersCountry | Only Estonian MID supported.  |
| MessagingMode  | Only asynchClientServer  supported.  |
| AsyncConfiguration | Not supported.  |
| ReturnDocInfo  | Not supported.  |
| ReturnDocData | Not supported.  |

#### **GetStatusInfo**

| **Parameter** | **Comment** | 
|---------------|-------------|
| WaitSignature | Not supported.  |

#### **RemoveDataFile**

| **Parameter** | **Comment** | 
|---------------|-------------|
| DataFileId |  Value is not "Dxx format, where xx stands for the sequence number." format. In SignedDocInfo created by Digital Signature Gateway service, the "Id" field on DataFileInfo is the fileName. |

#### **PrepareSignature**

**Response:**

| **Parameter** | **Comment** | 
|---------------|-------------|
| SignatureId   |  Value is not "Sxx format, where xx stands for the sequence number." format. Value in id-HASH format, where HASH is unique string for each signature. |

#### **FinalizeSignature**

| **Parameter** | **Comment** | 
|---------------|-------------|
| SignatureId   |  Value is not "Sxx format, where xx stands for the sequence number." format. Value in id-HASH format, where HASH is unique string for each signature. |

## Differences in error handling

DigiDocService uses jDigiDoc library as basis of its implementation. Digital Signature Gateway service uses DigiDoc4J as the base library. This means that error messages are not same between the services. If integrating e-service handles the error messages specifically then changes in implementation are needed.

In addition MID-REST service for Mobile-ID do not offer same statuses as DigiDocService offered for Mobile-ID. 

Here are examples of differences in SOAP error codes.

| **DigiDocService** | **Digital Signature Gateway service** | **Comment** | 
|--------------------|----------|-------------|
| 103 | No | Not supported. |
| 105 | No |  Not supported. |
| 201 | No | Not supported. |
| 202 | No |  Not supported. |
| 203 | No | Not supported. |
| 303 | 302 |  MID-Rest service sends always the same status. |
| 304 | 302 |  MID-Rest service sends always the same status. |
| 305 | 302 |  MID-Rest service sends always the same status. |
