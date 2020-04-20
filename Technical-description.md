---
title: Technical description
permalink: /technical-description/
layout: default
---
[Hashcode main usage flows](#hashcode-main-usage-flows)

[Authorization](#authorization)

[Hashcode Container form](#hashcode-container-form)

[Hashcode API description](#hashcode-api-description)

# Hashcode main usage flows

Riigi allkirjastamisteenus provides REST based interface for creating and signing ASIC-E based containers (this includes BDOC) in a hashcode form.

The service provides its API description in [WADL form](https://github.com/open-eid/SiGa/blob/develop/siga-webapp/src/main/resources/static/siga.wadl)

## Create hashcode container and sign with MID

![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Create_and_sign_MID.png) 

## Upload signed hashcode container and sign externally (with ID card)

![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Upload_and_sign_externally.png) 

## Validate hashcode container
1) Validate created container in session
2) Validate uploaded container in session 
3) Validate container without a session

![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/Validation.png) 

# Authorization

Each e-service must be registered in the Riigi allkirjastamisteenus service. Each e-service is issued a unique service identifier (UUID) and a signing secret key.

All requests sent to Riigi allkirjastamisteenus service must be signed with a previously agreed signing secret key that is unique to given e-service. The signature is calculated as an HMAC value over the mandatory "Authentication-X-..." headers and the request body contents. For requests that do not contain a payload the body contents are considered as an empty byte array.

In addition to the request payload, the e-service provider must send the following mandatory HTTP headers with every request:

* X-Authorization-Timestamp
* X-Authorization-ServiceUUID
* X-Authorization-Hmac-Algorithm (optional, defaults to "HmacSHA256")
* X-Authorization-Signature

The signature must be calculated before any transformations or compression is applied to the request body.

## Headers

Mandatory headers are same for all API requests. Content-Type may change in case of GET request.

| **Header** | **Mandatory** | **Default value** | **Description** | 
|------------|---------------|-------------------|-----------------|
| X-Authorization-Timestamp | + | - | Timestamp of request generation from client side. UTC timestamp of client system with precision 1 second. |
| X-Authorization-ServiceUUID | + | - |	Unique identifier of the relying party e-service. Used to determine client and e-service making the actual request. |
| X-Authorization-Signature | + | - | Hex encoded value of hmac hash of the canonicalized request data (authentication headers and body contents). |
| X-Authorization-Hmac-Algorithm | - | HmacSHA256 |	Algorithm used for Hmac calculation. Supported values: **HmacSHA256**, **HmacSHA384**, **HmacSHA512** |
| Content-Type | for POST and PUT only | - | The Media type of the body of the request (used with POST and PUT requests). Only supported value is application/json; charset=UTF-8 |

## Signature creation process

1) Given the following example container creation request:

~~~
POST https://siga.ria.ee/v1/hashcodecontainers HTTP/1.1
Accept-Encoding: gzip,deflate
Content-Type: application/json; charset=UTF-8
Content-Length: 370
Host: siga.ria.ee
Connection: Keep-Alive
User-Agent: Example service

{
    "dataFiles": [
        {
            "fileName": "test.txt",
            "fileHashSha512": "hQVz9wirVZNvP/q3HoaW8nu0FfvrGkZinhADKE4Y4j/dUuGfgONfR4VYdu0p/dj/yGH0qlE0FGsmUB2N3oLuhA==",
            "fileSize": "745",
            "fileHashSha256": "RnKZobNWVy8u92sDL4S2j1BUzMT5qTgt6hm90TfAGRo="
        }
    ]
}
~~~

2) Prepare the current timestamp and ServiceUUID headers for the request:

~~~
X-Authorization-Timestamp: 1551102625
X-Authorization-ServiceUUID: 13d03497-67bf-4879-8382-e8072ea04a09
~~~

3) Prepare the request url and extract the context path with query parameters
Note that the context path string should be extracted after URL Encoring rules are applied:

~~~
urlContextPath = "/hashcodecontainers?someParam=value%20with%20space"
~~~

4) Extract HTTP method name in uppercase

~~~
requestMethod = "POST"
~~~

5) Concatenate the values of X-Authorization-ServiceUUID header, X-Authorization-Timestamp header, requestMethod, urlContextPath and requestBody("UTF-8") with the delimiter ':' in the following manner to generate the plaintext for signature calculation:

~~~
plaintext = "13d03497-67bf-4879-8382-e8072ea04a09" + ":" + "1551102625" + ":" + "POST" + ":" + "/hashcodecontainers?someParam=value%20with%20space" + ":" + requestBody("UTF-8")
~~~

6) With the signing secret key, calculcate the HmacSHA256 value for given plaintext

~~~
signingSecret = "112233445566778899"
authSignature = HmacSHA256(plaintext, signigSecret)
~~~

7) Add the signature value to the request header X-Authorization-Signature to construct the final request

~~~
POST https://siga.ria.ee/v1/hashcodecontainers HTTP/1.1
Accept-Encoding: gzip,deflate
Content-Type: application/json; charset=UTF-8
Content-Length: 370
Host: siga.ria.ee
Connection: Keep-Alive
User-Agent: Example service
X-Authorization-Timestamp: 1551102625
X-Authorization-ServiceUUID: 13d03497-67bf-4879-8382-e8072ea04a09
X-Authorization-Hmac-Algorithm: HmacSHA256
X-Authorization-Signature: 5d57258d493b49bdaf0911e9aa20f5b9c1e25d6a1472779292828fe2ffd0518c

{
    "dataFiles": [
        {
            "fileName": "test.txt",
            "fileHashSha512": "hQVz9wirVZNvP/q3HoaW8nu0FfvrGkZinhADKE4Y4j/dUuGfgONfR4VYdu0p/dj/yGH0qlE0FGsmUB2N3oLuhA==",
            "fileSize": "745",
            "fileHashSha256": "RnKZobNWVy8u92sDL4S2j1BUzMT5qTgt6hm90TfAGRo="
        }
    ]
}
~~~

## Url encoding rules

* Do not URL encode any of the unreserved characters that RFC 3986 defines. These unreserved characters are A-Z, a-z, 0-9, hyphen ( - ), underscore ( _ ), period ( . ), and tilde ( ~ ).
* Percent encode extended UTF-8 characters in the form %XY%ZA.
* Percent encode the space character as %20 (and not +, as common encoding schemes do).
* Percent encode all other characters with %XY, where X and Y are hex characters 0-9 and uppercase A-F.

# Hashcode container form

BDOC and ASIC-E container needs to be converted to the hashcode form before sending to the Riigi allkirjastamisteenus service. In this form, the data file contents are replaced with their hash values.  
The hashcode form is not standard form, therefor, the containers must not be stored in this way. It is meant for transferring signatures and needed data to the Riigi allkirjastamisteenus service without the actual data files. 
This gives two benefits, its possible to handle containers with big data files and it conceals the content of the data files from Riigi allkirjastamisteenus service provider. 
Similarly, when the container is returned by the Riigi allkirjastamisteenus service, it should be converted back to regular form by inserting back the data file contents. Thus, in the end, the application provider still has a regular container that can be verified by standard tools (i.e., the DigiDoc4 client software). 

## Transforming container to hashcode form

1) Remove all signed files from the container. As the BDOC container is a ZIP-file, this can be accomplished with standard ZIP-file tools. The signed files are in the root folder of the ZIP file. **Folder structure for data files is not supported.** It is expected, that the datafiles are in the root of the folder.
2) Add hashes of the removed files into the container. Two hash files needs to be added, to the following locations in the container:
* META-INF/hashcodes-sha256.xml
* META-INF/hashcodes-sha512.xml

The first hash file contains the SHA-256 hashes of all of the signed files. Similarly, the second file contains SHA-512 hashes for the same files. 
Both files have the same format: for every signed file, filename of datafile, the hash of the file in Base64 and also the length of the file in bytes. (The XML-schema is below). Hash values are calculated directly on the file contents.

XML-schema for added hashcode files
~~~
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<xs:schema version="1.0" xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="hashcodes" type="hashcodesType"/>
  <xs:complexType name="fileEntryType">
    <xs:attribute name="full-path" type="xs:string" use="required"/>
    <xs:attribute name="hash" type="xs:string" use="required"/>
    <xs:attribute name="size" type="xs:long" use="required"/>
  </xs:complexType>
  <xs:complexType name="hashcodesType">
    <xs:sequence>
      <xs:element name="file-entry" type="fileEntryType" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>
</xs:schema>
~~~

The stucture of hashcode container should look like this:
~~~
├── META-INF
│   ├── **hashcodes-sha256.xml**
│   ├── **hashcodes-sha512.xml**
│   ├── manifest.xml
│   └── signatures0.xml
└── mimetype
~~~

## Transforming hashcode container back to standard form

When the HASHCODE container is returned back by service, analogous steps need to be carried out:

1) The data files need to be added back into the container (the container is in ZIP-format). 

*Notes*

The data files in the container need to have a ZIP-file comment about the BDOC-library. In practice, this comment may simply be copied from other files in the archive (for example, from the „mimetype"-file). Note that the signature files (META-INF/signatureN.xml) should keep their existing comments (this is to preserve information about the tools used for signing particular signatures). An example of such comment is the following:
* LIB DigiDocService/3.6.4 format: BDOC/2.1 Java: 1.7.0_51/Oracle Corporation OS: Windows 8/amd64/6.2 JVM: Java HotSpot(TM) 64-Bit Server VM/Oracle Corporation/24.51-b03

Some care must be taken to prevent accidental alterations to the file structure. Specifically, the ASiC standard (ETSI EN 319 162-1 V1.1.1) mandates the following about the "mimetype" file:

* "mimetype" shall be the first file in the ASiC container;
* "mimetype" shall not contain "Extra fields" in its ZIP header (i.e. extra field length at offset 28 shall be set to zero);
* "mimetype" shall not be compressed (i.e. compression method in its ZIP header at offset 8 shall be set to zero);
* the first 4 octets of the ASiC container file shall have the hex values: "50 4B 03 04". 

2) Remove all the hash files hashcodes-*.xml from the folder META-INF.

The stucture of container after conversion should look like this:
~~~
├── META-INF
│   ├── manifest.xml
│   └── signatures0.xml
│   └── signatures1.xml
├── mimetype
├── **file1.txt**
└── **File2.docx**
~~~

---
title: Hashcode API description
permalink: /hashcode-api-description/
layout: default
---

# Hashcode API description

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

