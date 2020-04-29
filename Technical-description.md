---
title: Technical description
permalink: /technical-description/
layout: default
---
| Table of contents |
|-------------------|
|[Introduction](#introduction)|
|[Usage flows from user perspective](#Usage-flows-from-user-perspective)|


## Introduction

Below are generalized examples how to implement user signing process in e-service. The examples are given based on [HWCrypto library](https://github.com/hwcrypto/hwcrypto.js)
usage in front end for ID-Card signing. The sequences and actions taken are only for demonstration, the actual flow and implementation should be based on integrating e-service
requirements and architecture.

## Usage flows from user perspective

### 1. Signing files with ID card from user point of view

1.1 User uploads files in e-service to be signed.

1.2 User inserts ID-Card to reader.

1.3 User presses sign with ID-Card button.

1.4 Select certificate prompt is presented, user selects correct certificate.

1.5 PIN prompt is presented, user inserts PIN2 and presses OK.

1.6 Signing process completed status is shown in e-service.

1.7 User downloads the signed container.

![](../img/ID_card_flow.png)

*(1) To start signature creation, signers certificate is needed in Riigi allkirjastamisteenus. In this example [HWCrypto getCertificate()](https://github.com/hwcrypto/hwcrypto.js/wiki/APIv2#getcertificate) is used to fetch the certificate.
It is important that **signing certificate** (nonrepudiation key-usage must be present) is fetched. Signing process can not be completed with authentication certificate. 

*(2) This step is needed by the [HWCrypto](https://github.com/hwcrypto/hwcrypto.js/wiki/APIv2#sign) as it requires hash value. Other signing libraries may require raw data, in that case dataToSign may only be base64 decoded
before sending to signing.

*(3) Signing with private key. In this example [HWCrypto sign()](https://github.com/hwcrypto/hwcrypto.js/wiki/APIv2#sign) is used. Conversion of hash algorithm form is required to match HWCrypto. 


### 2. Signing files with Mobile-ID

2.1 User uploads or chooses files in e-service to be signed.
2.2 User inserts ID-Card to reader.
2.3 User presses sign with Mobile-ID button.
2.4 User inserts personal identification code and mobile phone number and presses sign.
2.5 Verification code is shown in e-service.
2.6 PIN prompt is presented in users mobile together with verification code.
2.7 If verification code shown in e-service and mobile phone match, user inserts PIN2 and presses Ok.
2.7 Signing process completed status is shown in e-service.
2.7 User downloads the signed container.

### 3. Adding signature to already signed container

3.1 User uploads or chooses signed container in e-service to be signed.
3.2 User inserts ID-Card to reader.
3.3 User presses sign with ID-Card button.
3.4 Select certificate prompt is presented, user selects correct certificate.
3.5 PIN prompt is presented, user inserts PIN2 and presses OK.
3.6 Signing process completed status is shown in e-service.
3.7 User downloads the signed container.

