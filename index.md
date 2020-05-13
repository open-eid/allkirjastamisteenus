---
layout: default
---

## Welcome to the Digital Signature Gateway service (Riigi allkirjastamisteenus)!

Digital Signature Gateway service enables easy integration for all needed functionality to create digitally signed files.
Only one contract needed for all required functionality, no additional contracts for Mobile-ID, TimeStamping or other trust services.


![](/img/general.png)

Offered functionality:
* Creating ASICE/BDOC containers in HASHCODE form
* Signing with Estonian id-card/e-seal
* Signing with Estonian Mobile-ID
* Validating signed ASICE/BDOC/DDOC containers in HASHCODE form

Digital Signature Gateway service uses HASHCODE container form. This gives the following advantages:
* Confidenciality of signed data - the data files content is not sent to Digital Signature Gateway service. 
* Signed data file size is not relevant - large data files can be signed.

Digital Signature Gateway service advantages over implementing signing service locally:
* Platform independent API
* No need for extra contracts for Time Stamping, OCSP and Mobile-ID.
* Lower need of maintainence, API stays stable compared to the need of upgrades in signing libraries.

![](https://raw.githubusercontent.com/open-eid/SiGa/develop/docs/img/EL_Regionaalarengu_Fond_horisontaalne-vaike.jpg)