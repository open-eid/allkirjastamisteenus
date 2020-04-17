---
title: Authorization
permalink: /authorization/
layout: default
---

## Introduction

Each e-service must be registered in the SiGa service. Each e-service is issued a unique service identifier (UUID) and a signing secret key.

All requests sent to SiGa service must be signed with a previously agreed signing secret key that is unique to given e-service. The signature is calculated as an HMAC value over the mandatory "Authentication-X-..." headers and the request body contents. For requests that do not contain a payload the body contents are considered as an empty byte array.

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