---
title: JSON interface
permalink: /json-technical-description/
layout: default
---
| Table of contents |
|-------------------|
|[Introduction](#introduction)|
|[Access to the service](#access-to-the-service)|
|[Functionality](#functionality)|
|[Hashcode Container form](#hashcode-container-form)|
|[Hashcode API description](#hashcode-api-description)|

## Introduction

Digital Signature Gateway service (Riigi allkirjastamisteenus) is based on [Signature Gateway software](https://github.com/open-eid/SiGa), however it do not offer all the functionality present in the software.

Following chapters describe offered functionality.

## Access to the service

Accessing the service is restricted, it is needed to register for usage. See [Information System Authority](https://www.ria.ee/et/riigi-infosusteem/eid/partnerile.html#allkirjastamisteenus) webpage for registration info.

The authorization mechanism is described [here](https://github.com/open-eid/SiGa/wiki/Authorization). Upon registration service specific UUID and signing secret are provided through secure channel.

## Functionality

| **Functionality** | **Signature Gateway software ** | **Digital Signature Gateway service JSON** |  **Comment** | 
|-------------------|--------------------|----------|--------------|
| Container creation | Yes | Yes | Only **hashcode** form. |
| Manipulation of signable content | Yes | Yes | Only **hashcode** form "data files". |
| Adding signatures | Yes | Yes | Signatures created with Estonian certificates. |
| Support for BDOC and ASICE containers | Yes | Yes | Only **hashcode** form. |
| Signing with external device | Yes | Yes | ID card, e-seal, ... (certificate must be in Estonian TSL). |
| Signing with Mobile-ID | Yes | Yes | Only Estonian Mobile-ID. |
| Signing with Smart-ID | Yes | Yes | Only QSCD level accounts are supported (accounts created after 08.11.2018). |
| Signature validation | Yes | Yes | Validation of DDOC containers in **hashcode** form is also supported. Keep in mind that DDOC hashcode form differs from "standard" hashcode form. |

## Hashcode container form

Digital Signature Gateway service supports only hashcode based data files manipulation. This means that signed data files are not leaving the integrating e-service premises giving protection to the content. 
In addition this enables to sign large data files as hashcode representation of the file is not tied to the size of real file.

The details how to convert ASICE/BDOC containers to and from hashcode form can be found [here.](https://github.com/open-eid/SiGa/wiki/Hashcode-container-form)

For validation of DDOC containers in hashcode form different conversion must be made. This is described [here.](../ddoc-hashcode-form)

## Hashcode API description

Digital Signature Gateway service supports subset of API methods supported by [Signature Gateway software](https://github.com/open-eid/SiGa). In below table supported methods are listed. Refer Signature Gateway documentation for details.

| **Action** | **API specifics** | **Comment** |
|------------|-------------------|-------------|
| Create container | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#creating-container) | Creating a container from hashcode files |
| Upload container | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#upload-container) | Uploading a signed hashcode container |
| Add data file | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#add-datafiles-to-unsigned-container) | Adding hashcode representation of data file to container |
| Get data file list | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#get-data-files-list) | Retrieving list of hashcode representations of data files |
| Delete data file | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#delete-datafile-from-unsigned-container) | Deleting hashcode representation of data file from container |
| Start remote signing | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#start-remote-signing) | Initiating signing process |
| Finish remote signing | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#finalize-remote-signing) | Finalize signing process |
| Start Mobile-ID signing | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#start-mobile-id-signing) | Initiating Mobile-ID signing process |
| Get Mobile-ID signing status | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-mobile-id-signing-status) | Getting Mobile-ID signing process status |
| Trigger certificate selection for Smart-ID signing | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#trigger-certificate-selection-for-smart-id-signing) | Requesting certificate to be used in signing process |
| Get Smart-ID certificate selection status | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-certificate-selection-status) | Getting status for certificate choice |
| Start Smart-ID signing | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#start-smart-id-signing) | Initiating Smart-ID signing process |
| Get Smart-ID signing status | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-smart-id-signing-status) | Getting Smart-ID signing process status|
| Get signature list | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-signature-list-of-given-hashcode-container) | Retrieving signers list |
| Get signers information | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-signer-info-on-given-signature) | Retrieving signers information |
| Validate container in session | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-validation-of-hashcode-container-in-session) | Validating container in session |
| Validate container without session | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-validation-of-hashcode-container-without-session) | Validating container without session |
| Get container | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-hashcode-container) | Retrieve hashcode container |
| Delete container | [See details](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#delete-hashcode-container) | Delete hashcode container in session |