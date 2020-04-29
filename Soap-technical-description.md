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
|[Differences in protocol](#differences-in-protocol)|
|[Differences in error handling](#differences-in-error-handling)|

## Introduction

Riigi allkirjastamisteenus offers a SOAP based interface for **legacy systems** currently using DigiDocService. It is **strongly recommended** to use JSON based api for integration even if you currently use DigiDocService.

SOAP interface is based on SK ID Solutions AS [DigiDocService API](http://sk-eid.github.io/dds-documentation/) however, it do not implement the full functionality.
Current e-service integration needs to be analyzed in regards of suitability for switching to Riigi allkirjastamisteenus and some changes in integration may be required.

If required changes are substantial we suggest integrating JSON interface instead of SOAP interface.

For analyzing whether the Riigi allkirjastamisteenus SOAP interface is suitable for your integration please revise the below chapters.

## Access to the service

Accessing the service is IP based. See [Integration process](../integration-process) page for information how to gain access to service.

## Differences in functionality

| **Functionality** | **DigiDocService** | **Riigi allkirjastamisteenus** |  **Comment** | 
|-------------------|--------------------|----------|--------------|
| Container creation | Yes | Yes | Creating new containers |
| Adding signatures | Yes | Yes | Adding signatures to signed containers |
| Support for BDOC and ASICE containers | Yes | Yes | Riigi allkirjastamisteenus supports the hashcode form. No datafiles support. |
| Support for DDOC container | Yes | **No** | Not possible to use |
| Container hashcode form | Yes | Yes | Same hashcode format |
| Signing with external device | Yes | Yes | ID card, e-seal, ... (certificate must be in Estonian TSL) |
| Signing with Mobile-ID | Yes | Yes | Riigi allkirjastamisteenus supports only Estonian Mobile-ID |
| Authentication with Mobile-ID | Yes | **No** | Riigi allkirjastamisteenus is purely signing/container service, use TARA for authentication |
| Verification of certificate validity | Yes | **No** | Not possible to validate |
| Signature validation | Yes | Yes | Riigi allkirjastamisteenus uses Valideerimisteenus (SIVA) for validation |


## Differences in protocol

Riigi allkirjastamisteenus only supports Digital Signature API. No methods for authentication are supported.

### Method differences 

| **Method** | **DigiDocService** | **Riigi allkirjastamisteenus** | **Comment** | 
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
| DataFileId |  Value is not "Dxx format, where xx stands for the sequence number." format. In SignedDocInfo created by Riigi allkirjastamisteenus, the "Id" field on DataFileInfo is the fileName. |

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

DigiDocService uses jDigiDoc library as basis of its implementation. Riigi allkirjastamisteenus uses DigiDoc4J as the base library. This means that error messages are not homogenous.

If integrating e-service handles the error messages specifically then changes in implementation are needed.


