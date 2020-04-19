---
title: Hashcode API description
permalink: /hashcode-api-description/
layout: default
---

## Introduction

This document describes the methods used for hashcode container manipulation. 

## Creating container

Method for creating container structure.

| **Method** | **URL** | 
|------------|---------|
| POST | /hashcodecontainers |

### Request parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| dataFiles | Array | + | Array containing data file objects. |
| dataFiles[0] | Object | + | Object containing data files information. |
| dataFiles[0].fileName | String | + | Name of the data file. **Folder structure for data files is not supported.** |
| dataFiles[0].fileHashSha256 | String | + | SHA256 hash of the data file in Base64. |
| dataFiles[0].fileHashSha512 | String | + | SHA512 hash of the data file in Base64. |
| dataFiles[0].fileSize | Integer | + | size of the file in bytes. |


### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| containerId | String | + | Container ID that is used to identify the container in use. |

## Upload container

Method for uploading hashcode container.

| **Method** | **URL** | 
|------------|---------|
| POST | /upload/hashcodecontainers |

### Request parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| container | String | + | Base64 encoded container. |


### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| containerId | String | + | Container ID that is used to identify the container in use. |

## Add datafiles to unsigned container

Method for adding data files to container. **NB! It is possible to add data files only to unsigned container!**

| **Method** | **URL** | 
|------------|---------|
| POST | /hashcodecontainers/{containerId}/datafiles |

### Request parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| dataFiles | Array | + | Array containing data file objects. |
| dataFiles[0] | Object | + | Object containing data files information. |
| dataFiles[0].fileName | String | + | Name of the data file. **Folder structure for data files is not supported.** |
| dataFiles[0].fileHashSha256 | String | + | SHA256 hash of the data file in Base64. |
| dataFiles[0].fileHashSha512 | String | + | SHA512 hash of the data file in Base64. |
| dataFiles[0].fileSize | Integer | + | size of the file in bytes. |

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| result | String | + | OK on succes. |

## Get data files list

Method for retrieving container data file list.

| **Method** | **URL** | 
|------------|---------|
| GET | /hashcodecontainers/{containerId}/datafiles |

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| dataFiles | Array | + | Array containing data file objects. |
| dataFiles[0] | Object | + | Object containing data files information. |
| dataFiles[0].fileName | String | + | Name of the data file. |
| dataFiles[0].fileHashSha256 | String | + | SHA256 hash of the data file in Base64. |
| dataFiles[0].fileHashSha512 | String | + | SHA512 hash of the data file in Base64. |
| dataFiles[0].fileSize | Integer | + | size of the file in bytes. |

## Delete datafile from unsigned container

Method for deleting data files from container. **NB! It is possible to remove data files only from unsigned container!**

| **Method** | **URL** | 
|------------|---------|
| DELETE | /hashcodecontainers/{containerId}/datafiles/{fileName} |

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| result | String | + | OK on succes. |

## Start remote signing

Method for generating data to be signed externally

| **Method** | **URL** | 
|------------|---------|
| POST | /hashcodecontainers/{containerId}/remotesigning |

### Request parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| signingCertificate | String | + | DER encoded X.509 signers certificate in Base64. |
| signatureProfile | String | + | Profile of the signature. Available values **LT** - TimeStamp based and **LT_TM** - TimeMark based signatures. |
| roles | Array | - | Array of strings describing the signer role. |
| signatureProductionPlace | object | - | Object containing signing location. |
| signatureProductionPlace.countryName | String | - | Signing country. |
| signatureProductionPlace.city | String | - | Signing city. |
| signatureProductionPlace.stateOrProvince | String | - | Signing state or province. |
| signatureProductionPlace.postalCode | String | - | Signing postal code. |

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| dataToSign | String | + | Data to be signed on client side. XML structure to be signed encoded in Base64.  |
| digestAlgorithm | String | + | Digest algorithm used to calculate dataToSign. Service uses **SHA512**. |
| generatedSignatureId| String | + | Signature ID needed for finalization of signature. |

## Finalize remote signing

Method for returning signature value to service

| **Method** | **URL** | 
|------------|---------|
| PUT | /hashcodecontainers/{containerId}/remotesigning/{generatedSignatureId} |

### Request parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| signatureValue | String | + | Signature value created by client encoded in Base64. Signature must be created using the algorithm returned in POST /hashcodecontainers/{containerId}/remotesigning response (**SHA512**). |

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| result | String | + | OK is returned. |

## Start Mobile-ID signing

Method for starting Mobile-ID signing of container

| **Method** | **URL** | 
|------------|---------|
| POST | /hashcodecontainers/{containerId}/mobileidsigning |

### Request parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| personIdentifier | String | + | Signers personal national identification code. |
| phoneNo | String | + | Signers phone number with **+372** prefix. |
| language | String | + | Language for user dialog in mobile phone. 3-character capitalized acronyms are used. Possible values: **ENG**, **EST**, **RUS**, **LIT**. |
| signatureProfile | String | + | Profile of the signature. Available values **LT** - TimeStamp based and **LT_TM** - TimeMark based signatures. |
| roles | Array | - | Array of strings describing the signer role. |
| messageToDisplay | String | - | Text displayed in addition to ServiceName and before asking authentication PIN. Maximum length is 40 bytes. In case of Latin letters, this means also a 40 character long text, but Cyrillic characters may be encoded by two bytes and you will not be able to send more than 20 symbols. |
| signatureProductionPlace | object | - | Object containing signing location. |
| signatureProductionPlace.countryName | String | - | Signing country. |
| signatureProductionPlace.city | String | - | Signing city. |
| signatureProductionPlace.stateOrProvince | String | - | Signing state or province. |
| signatureProductionPlace.postalCode | String | - | Signing postal code. |

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| challengeId | String | + | 4-character control code calculated on basis of the Challenge value to be signed. This code is displayed on mobile phone's screen and must be also displayed by integrating web service in order to ensure the user on authencity of the query. |
| generatedSignatureId| String | + | Signature ID needed for finalization of signature. |

In case of not MID user and revoked/suspended certificate error is returned.

## Request Mobile-ID signing status

Method for requesting status of the signing process (signer to enter a PIN on the mobile device). This method should be polled with interval while the status is OUTSTANDING_TRANSACTION in all other statuses polling should be stopped.

| **Method** | **URL** | 
|------------|---------|
| GET | /hashcodecontainers/{containerId}/mobileidsigning/{generatedSignatureId}/status |

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| midStatus | String | + |  <ul><li>SIGNATURE – signature was successfully created;</li><li>OUTSTANDING_TRANSACTION – authentication is still on the way, the status query shall be repeated;</li><li>EXPIRED_TRANSACTION – service timed out before user managed to complete the signing;</li><li>USER_CANCEL – the user refused or cancelled the signing proces;</li><li>MID_NOT_READY – the Mobile-ID of the SIM is not yet ready for the operations;</li><li>INTERNAL_ERROR – technical error.</li><li>NOT_VALID - signature is not valid</li><li>SENDING_ERROR – other error when sending message (phone is incapable of receiving the message, error in messaging server etc.);</li><li>SIM_ERROR – SIM application error;</li><li>PHONE_ABSENT – phone is switched off or out of coverage;</li></ul> |

## Request signature list of given hashcode container

Method for returning all the signers of the container.

| **Method** | **URL** | 
|------------|---------|
| GET | /hashcodecontainers/{containerId}/signatures	|

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| signatures | Array | - | Array of signature objects. |
| signatures[0] | Object | + | Object containing signature information.  |
| signatures[0].id | String | + | Signature id taken from signature. |
| signatures[0].generatedSignatureId | String | + | Unique signature id generated by the service to enable manipulation of signatures.  |
| signatures[0].signerInfo | String | + | Signers certificate DN field. Example "SERIALNUMBER=PNOEE-38001085718, GIVENNAME=JAAK-KRISTJAN, SURNAME=JÕEORG, CN=\"JÕEORG,JAAK-KRISTJAN,38001085718\", C=EE".  |
| signatures[0].signatureProfile | String | + | Signature profile. |

## Request signer info on given signature

Method for returning all the signers of the container.

| **Method** | **URL** | 
|------------|---------|
| GET | /hashcodecontainers/{containerId}/signatures/{signatureId}	|

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| id | String | + | Signature id taken from signature |
| signerInfo | String | + | Signers certificate DN field. Example "SERIALNUMBER=PNOEE-38001085718, GIVENNAME=JAAK-KRISTJAN, SURNAME=JÕEORG, CN=\"JÕEORG,JAAK-KRISTJAN,38001085718\", C=EE".  |
| signatureProfile | String | + | Signature profile. |
| ocspCertificate | String | - | Certificate of OCSP provider. |
| timeStampTokenCertificate | String | - | Certificate of timestamp provider. |
| ocspResponseCreationTime | String | - | OCSP creation time. |
| timeStampCreationTime | String | - | Timestamp creation time. |
| trustedSigningTime | String | - | Time considered as trusted signature creation time. Can be either timestamp or OCSP time, depending on signature profile. |
| signingCertificate | String | + | Signers certificate. |
| claimedSigningTime | String | + | Computer time on signature creation. This is not trusted signing time! |
| roles | Array | - | Signer roles.  |
| signatureProductionPlace | object | - | Object containing signing location. |
| signatureProductionPlace.countryName | String | - | Signing country. |
| signatureProductionPlace.city | String | - | Signing city. |
| signatureProductionPlace.stateOrProvince | String | - | Signing state or province. |
| signatureProductionPlace.postalCode | String | - | Signing postal code. |

## Request validation of hashcode container in session

Method for retrieving validation report (simple report from SIVA service is returned).

**NB! validation of LTA level signatures is not supported in hashcode form** 

| **Method** | **URL** | 
|------------|---------|
| GET | /hashcodecontainers/{containerId}/validationreport	|

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| validationConclusion | Object | + | Check [SIVA validationConclusion block](http://open-eid.github.io/SiVa/siva3/interfaces/#validation-response-parameters-simple-report-successful-scenario) |

## Request validation of hashcode container without session

Method for validating a hashcode container.

| **Method** | **URL** | 
|------------|---------|
| POST | 	/hashcodecontainers/validationreport |

### Request parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| container | String | + | Base64 encoded container. |


### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| validationConclusion | Object | + | Check [SIVA validationConclusion block](http://open-eid.github.io/SiVa/siva3/interfaces/#validation-response-parameters-simple-report-successful-scenario) |

## Request hashcode container

Method for retrieving the container.

| **Method** | **URL** | 
|------------|---------|
| GET | /hashcodecontainers/{containerId}	|

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| container | String | + | Base64 encoded container. |

## Delete hashcode container

Method for deleting hashcode container in use. This closes the active "session". It is recommended to delete container after usage as unclosed sessions may max out allowed connections count for the service.

| **Method** | **URL** | 
|------------|---------|
| DELETE | /hashcodecontainers/{containerId}	|

### Response parameters

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| result | String | + | OK is returned |

## Error responses

| **Parameter** | **Type** | **Mandatory** | **Description** | 
|---------------|----------|---------------|-----------------|
| errorCode | String | + | Error code |
| errorMessage | String | + | Error description  |

