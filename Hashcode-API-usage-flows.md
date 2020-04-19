---
title: Hashcode API usage flows
permalink: /hashcode-api-usage-flows/
layout: default
---

## Introduction

SIGA provides REST based interface for creating and signing ASIC-E based containers (this includes BDOC) in a hashcode form.

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