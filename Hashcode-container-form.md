---
title: Hashcode container form
permalink: /hashcode-container-form/
layout: default
---

## Introduction

BDOC and ASIC-E container needs to be converted to the hashcode form before sending to the SIGA service. In this form, the data file contents are replaced with their hash values.  
The hashcode form is not standard form, therefor, the containers must not be stored in this way. It is meant for transferring signatures and needed data to the SIGA service without the actual data files. 
This gives two benefits, its possible to handle containers with big data files and it conceals the content of the data files from SIGA service provider. 
Similarly, when the container is returned by the SIGA service, it should be converted back to regular form by inserting back the data file contents. Thus, in the end, the application provider still has a regular container that can be verified by standard tools (i.e., the DigiDoc4 client software). 

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