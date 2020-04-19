---
title: Best practices
permalink: /best-practices/
layout: default
---
## Introduction

Interfacing with SiGa is easy. This document describes some practices for web applications integrating with SiGa service.

## Security

* Web applications, that enable digital signing using Mobile-ID or ID-card, should use encrypted channel (HTTPS) between browser and web server.
* Web application should trusts SiGa service TSL certificate. It means that web application should use truststore, which contains SiGa service certificate.
* Sensitive information must be handled with care. For example, SiGa signing secret key, truststore passwords etc must be kept secret.

## Prevent spamming

Web service needs to poll [Mobile-ID signing status](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-mobile-id-signing-status) with interval. It is recommended that polling delay between Mobile-ID status requests are 2-10 seconds. 

## Showing verification code to end-user

Application, that enables users to digitally sign documents using Mobile-ID, must clearly present verification code to the user and warn the user to check if challenge number presented by application is the same as challenge number on mobile phone screen.
If challenge numbers differ, Mobile-ID operation has to be cancelled by the user. Challenge number should be correctly implemented and highly visible also when Mobile-ID operations are used from mobile device.
Verification code can be found from Start Mobile-ID signing response under challengeId: [Start Mobile-ID response](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#response-parameters-7).

## Signature validation

It is recommended to validate the signature after signature creation for extra confirmation. For that, use endpoint : [Get validation report](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-validation-of-hashcode-container-in-session). 
validSignaturesCount must match the signaturesCount value. For determining the status of each signers signature, signatures indication value can be checked. Signature is valid when the indication is TOTAL-PASSED.

## Handling error codes

For most cases, it is not needed to show specific error codes from SiGa to end users, these cases can be handled by web applications internally. However, Mobile-ID requires interaction with user and it is needed to present a suitable information to user.

Handling Mobile-ID responses: 
* [Start Mobile-ID signing](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#start-mobile-id-signing): It is recommended to present a suitable error message to the user when error code is MID_EXCEPTION and error message is NOT_FOUND or NOT_ACTIVE_FAULT. This error is returned when phone number and id code combination do not return any Mobile-ID user. This can be due mistake on inputting the values or the certificates have been suspended/revoked.
* [Request Mobile-ID signing status](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#request-mobile-id-signing-status): It is recommended to present a suitable error message to the user when midStatus is not SIGNATURE.

## Container deletion

It is recommended to delete container from session after finishing the required actions. This prevents reaching the maximum connection limits. For that, use endpoint: [Delete hashcode container](https://github.com/open-eid/SiGa/wiki/Hashcode-API-description#delete-hashcode-container).
