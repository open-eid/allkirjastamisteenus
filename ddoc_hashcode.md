---
title: DDOC hashcode form
permalink: /ddoc-hashcode-form/
layout: default
---

## Converting DDOC container to hashcode form

Data file in DDOC container is represented with following DataFile element:

```
<DataFile xmlns="http://www.sk.ee/DigiDoc/v1.3.0#" ContentType="EMBEDDED_BASE64" Filename="test.txt" Id="D0" MimeType="text/plain" Size="42">VGhpcyBpcyBhIHRlc3QgZmlsZQ0Kc2Vjb25kbGluZQ0KdGhpcmRsaW5l</DataFile>
```

It must be replaced with the following hashcode representation element:
```
<DataFile xmlns="http://www.sk.ee/DigiDoc/v1.3.0#" ContentType="HASHCODE" Filename="test.txt" Id="D0" MimeType="text/plain" Size="42" DigestType="sha1" DigestValue="t8eRSrKTgR4PAAKTLYWGCjuTSJA="></DataFile>
```

* ContentType must be changed to "Hashcode".
* DigestType="sha1" must be added.
* DigestValue="t8eRSrKTgR4PAAKTLYWGCjuTSJA=" must be added. Sha1 hash of data file must be calculated and set.
* Data file base64 content must be removed.

