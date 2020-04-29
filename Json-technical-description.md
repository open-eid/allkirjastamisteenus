---
title: JSON interface
permalink: /json-technical-description/
layout: default
---
| Table of contents |
|-------------------|
|[Introduction](#introduction)|
|[Hashcode main usage flows](#hashcode-main-usage-flows)|
|[Authorization](#authorization)|
|[Hashcode Container form](#hashcode-container-form)|
|[Hashcode API description](#hashcode-api-description)|

## Introduction

Riigi allkirjastamisteenus is based on [Signature Gateway software](https://github.com/open-eid/SiGa), however it do not offer all the functionality present in the software.

Following chapters describe offered functionality.

## Functionality

| **Functionality** | **Signature Gateway ** | **Riigi allkirjastamisteenus** |  **Comment** | 
|-------------------|--------------------|----------|--------------|
| Container creation | Yes | Yes | Only **hashcode** form. |
| Manipulation of signable content | Yes | Yes | Only **hashcode** form "data files". |
| Adding signatures | Yes | Yes | Signatures created with Estonian certificates. |
| Support for BDOC and ASICE containers | Yes | Yes | Only **hashcode** form. |
| Signing with external device | Yes | Yes | ID card, e-seal, ... (certificate must be in Estonian TSL). |
| Signing with Mobile-ID | Yes | Yes | Only Estonian Mobile-ID. |
| Signing with Smart-ID | Yes | **No** | No support. |
| Signature validation | Yes | Yes | Validation of DDOC containers in **hashcode** form is also supported. |

