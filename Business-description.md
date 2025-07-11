---
title: Business description
permalink: /business-description/
layout: default
---
## How does Digital Signature Gateway service work?

Digital Signature Gateway service is a back end service that interacts with other information services and systems. It means Digital Signature Gateway service provides the functionality of creation and validation of electronic signatures, but does not offer any graphical user interface to end users. Thus, the graphic design and screen layout presented to end users, signers, will vary across the customers of Digital Signature Gateway service (i.e. other information systems).

To ensure confidentiality, Digital Signature Gateway service processes the fingerprint of a document, also known as [hash](https://sectigostore.com/blog/what-is-a-digital-signature-and-how-does-the-digital-signature-process-work/), and not the document itself.
The simplified document signing steps are as follows:
1. Digital Signature Gateway service receives the hash value and basic signature of a document to be signed.
2. Digital Signature Gateway service interacts with [trust services](https://www.id.ee/en/article/trust-services-what-are-they/) to collect the details that are mandatory elements of a [qualified electronic signature](https://www.id.ee/en/article/electronic-signatures-and-addressing-them-in-europe/), e.g. the timestamp and the validity confirmation of the signer’s [certificate](https://www.id.ee/en/article/certificates-what-are-they-and-why-do-i-need-them/).
3. Digital Signature Gateway service returns the correctly signed container to the signer.

Our service supports signature creation with Estonian ID card/e-Seal, Estonian Mobile ID or Estonian Smart ID.

## What can I do with Signature Gateway service?

Once your information system is integrated with Digital Signature Gateway service, you can
* create containers in [ASiC-e format](https://www.id.ee/en/article/bdoc-cdoc-and-asice-digidoc-file-formats-4/)
* add your electronic signature to a container holding any type of digital data (documents, spreadsheets, multimedia content etc.)
* sign your data using a qualified signature creation device or a qualified signature creation mobile app, such as:
  * [Estonian ID card](https://www.id.ee/en/article/id-card-and-its-uses/)
  * [Estonian e-Seal](https://www.id.ee/en/article/we-recommend-using-an-e-seal-instead-of-mass-signing-with-the-id-card/)
  * [Mobile ID](https://www.id.ee/en/article/using-mobile-id/)
  * [Smart ID](https://www.smart-id.com/)
* validate signed ASiC-e, BDOC, and DDOC containers in the HASHCODE form

## Whom is Digital Signature Gateway service for?

Digital Signature Gateway service is offered to all public sector organisations [listed](https://www.fin.ee/sites/default/files/documents/2023-02/Avaliku_sektori_asutused_asutuse_liikide_loikes%202022.xlsx) (298.93 KB, XLSX) by [the Ministry of Finance of the Republic of Estonia](https://www.fin.ee).

## What does Digital Signature Gateway service cost?

The service is currently free for the above specified target audiences.

## What are the key benefits that we offer?

Digital Signature Gateway service offers unified API for container and signature creation. API is platform independent and can be used in any e-service capable of handling JSON/REST API.

E-service maintenance is limited to the updates for API integration.

We keep our service up-to-date with security patches and ensure compliance with [eSignature standards](https://ec.europa.eu/digital-building-blocks/sites/display/DIGITAL/Standards+and+specifications). 

Customers of Digital Signature Gateway service need to set up only one contract with RIA to access digital signing and validation services. No extra integration with Mobile ID, Smart ID, or trusted services are required.

Digital Signature Gateway service uses the HASHCODE container form, which ensures
* confidentiality of the signed data – the service neither processes nor stores the content of the signed data
* no maximum limit on the size of the signed data

## How can I join Digital Signature Gateway service?

To join our service, you need to go through a two-step procedure:
1. Register your e-service in our demo environment and conduct Digital Signature Gateway service integration tests.
2. Register your e-service in our production environment.
You can find [application forms](https://www.ria.ee/riigi-infosusteem/elektrooniline-identiteet-ja-usaldusteenused/digiallkirja-serverteenused#allkirjastamisteenus) on RIA website. Submit your completed applications at klient@ria.ee. 

## Can I use Digital Signature Gateway service if my organisation is not part of the Estonian public sector?

Our service is currently only provided to the public sector organisations of the Republic of Estonia. However, the source code of Digital Signature Gateway service is [freely available](https://github.com/open-eid/SiGa/wiki) for anyone to use and to create your own digital signing solution.

## Additional questions?

Feel free to contact us at klient@ria.ee if you have any questions about Digital Signature Gateway service.
