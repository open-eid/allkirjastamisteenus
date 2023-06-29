---
title: Technical description
permalink: /technical-description/
layout: default
---
| Table of contents |
|-------------------|
|[Introduction](#introduction)|
|[Usage flows from user perspective](#usage-flows-from-user-perspective)|
|[Implementations needed in e-service](#implementations-needed-in-e-service)|
|[Testing the integration](#testing-the-integration)|

## Introduction

Below are generalized examples of user signing process in an e-service. ID-card signing flows use [Web eID library](https://github.com/web-eid/web-eid.js) for
front-end communication with ID-card. The sequences in this chapter are for demonstration, the actual flow and implementation should be based on integrating e-service
requirements and architecture.

The example code in JAVA with explicit API demonstration can be found [here](https://github.com/open-eid/SiGa/tree/master/siga-sample-application) together with [instructions how to run the Signature Gateway with Docker](https://github.com/open-eid/SiGa#running-siga-with-docker) for learning purposes.

## Usage flows from user perspective

### 1. Signing files with ID card from user point of view

In this example e-service handles all actions regarding to-be-signed data files before opening session with Digital Signature Gateway service (Riigi allkirjastamisteenus).

1.1 User selects files to-be-signed in e-service.

1.2 User inserts ID-Card to reader.

1.3 User presses "sign with ID-Card" button.

1.4 Select certificate prompt is presented, user selects correct certificate.

1.5 PIN prompt is presented, user inserts PIN2 and presses OK.

1.6 User checks that signing was successful.

1.7 User downloads the signed container.

![](../img/ID_card_flow.png)

*(1) Digital Signature Gateway service needs signers certificate for signature creation. In this example, [Web eID's getSigningCertificate()](https://github.com/web-eid/web-eid.js#get-signing-certificate) method is used to fetch the certificate.
It is important that **signing certificate** (nonrepudiation key-usage must be present) is fetched. Signing process can not be completed with authentication certificate. 

*(2) This step is needed by the [Web eID](https://github.com/web-eid/web-eid.js#sign) as it requires hash value. Other signing libraries may require raw data, in that case dataToSign may only be base64 decoded
before sending to signing.

*(3) Signing with private key. In this example [Web eID's sign()](https://github.com/web-eid/web-eid.js#sign) is used. Conversion of hash algorithm's name is required to match an [allowed hashFunction value for Web eID](https://github.com/web-eid/web-eid.js#sign-parameters). 


### 2. Signing files with Mobile-ID

2.1 User uploads to-be-signed file into e-service.

2.2 User removes to-be-signed file.

2.3 User uploads to-be-signed file into e-service.

2.4 User presses "sign with Mobile-ID" button.

2.5 User enters personal identification code and mobile phone number and presses "sign".

2.6 User checks verification code in e-service.

2.7 PIN prompt is presented in users mobile together with verification code.

2.8 If verification code shown in e-service and mobile phone match, user inserts PIN2 and presses Ok.

2.9 User checks that signing was successful.

2.10 User moves to next step in e-service flow.

![](../img/MID_flow.png)


### 3. Adding signature to already signed container

3.1 User uploads signed container into e-service.

3.2 User verifies the signature status.

3.3 User starts signing process (step 1.2 or 2.4 in previous examples) to add signature to container.

![](../img/Add_signature_flow.png)

## Implementations needed in e-service

In order to integrate with Digital Signature Gateway service following functionality must be implemented in e-service.

### Authorization

To access the service authorization must be implemented. The specifics are [described here.](https://github.com/open-eid/SiGa/wiki/Authorization)

### API integration

Digital Signature Gateway service offers [following methods](https://open-eid.github.io/allkirjastamisteenus/json-technical-description/#hashcode-api-description).

### Calculating data file hashes

Digital Signature Gateway service supports only hashcode form. Therefor ability to calculate data file hashes is needed. The integrating service needs to calculate SHA256 and SHA512 hashes of data files.

### Converting container to and from hashcode form

Digital Signature Gateway service handles containers in hashcode form. Integrating service needs to be able to manipulate the container content by adding and removing files in it. ASICE/BDOC container is 
ZIP container, it can be manipulated with standard ZIP libraries. However, special care must be taken on some aspects of the container [described here.](https://github.com/open-eid/SiGa/wiki/Hashcode-container-form)

### Integration with signature creation devices

To use remote signing in Digital Signature Gateway service, integrating service must implement retrieving public certificate and signing hashes with the signature creation device. 
The integrations required are signature creation device specific (for example it can be e-seal using PKCS11 or ID-Card with Web eID) 

### Additional consideration

Please check [here](https://github.com/open-eid/SiGa/wiki/Best-practices) for additional implementation considerations.

## Testing the integration

Successful integration with [Demo environment](https://www.ria.ee/riigi-infosusteem/elektrooniline-identiteet-ja-usaldusteenused/digiallkirja-serverteenused#allkirjastamisteenus) is requirement for using Digital Signature Gateway service in production.
For development and learning purposes it is possible to set up the Signature Gateway software using [Docker](https://github.com/open-eid/SiGa#running-siga-with-docker) on local premises.
However, Digital Signature Gateway service does not offer Signature Gateway full functionality. There may also be version differences between service and available software. 
For acceptance testing it is recommended to always use Demo environment.

**NB!** It is only possible to test with TEST certificates in DEMO environment. This includes ID-Cards, Mobile-ID, e-Seals, etc. Signing certificate issuer must be present in [TEST TSL](https://github.com/open-eid/test-TL/blob/master/EE_T.xml) to be accepted by 
Digital Signature Gateway service. You can order TEST ID-Card/TEST e-Seal [here](https://www.skidsolutions.eu/en/services/testcard/) and use Demo Mobile-ID numbers from [here](https://github.com/SK-EID/MID/wiki/Test-number-for-automated-testing-in-DEMO).
You can activate and/or change the status of your TEST certificates in [here](https://demo.sk.ee/upload_cert/) to test signing with revoked/unknown certificate.
Uploading your LIVE certificate to DEMO OCSP service or activating your LIVE Mobile-ID in DEMO MID service do NOT enable to usage of LIVE certificates in Digital Signature Gateway service. 

### Special considerations for testing

* Authorization - be sure to check that correct [URL encoding rules](https://github.com/open-eid/SiGa/wiki/Authorization#url-encoding-rules) and charset encoding is used. Verify that different [HMAC algorithms](https://github.com/open-eid/SiGa/wiki/Authorization#headers) are supported.
* Hashcode container form - Make sure that all data files are removed from hashcode container before sending to service. Ensure that the final container is converted correctly to normal form by using DigiDoc4 client as reference (can not be used with test certificates for signature validation but it should not give any container errors).
* Mobile-ID signing - Ensure that challengeID/verification code is correctly shown to customer. Test the negative cases ([Mobile-ID testnumbers can be found here](https://github.com/SK-EID/MID/wiki/Test-number-for-automated-testing-in-DEMO)) and ensure that user is given correct feedback. Also test timeouts as signing with Mobile-ID can take time. Ensure that user can not activate signing several times (by clicking the sign button repeatedly) on one signing process.
* Signing with ID card - Test insertion of card in different steps of the signing flow (before clicking sign, after clicking sign...). Ensure that user can not activate signing several times (by clicking the sign button repeatedly) on one signing process.
* Validation result - Test both valid and invalid containers to ensure that correct result is shown for user.
