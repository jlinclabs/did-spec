---
title: DID Method Specification
type: single
---

<h1 class="firsthead">JLINC DID Method Specification</h1>

<p class="nomargin">version 1.0 - October 12, 2018</p>  

### _Abstract_

JLINC is a protocol for sharing data protected by an agreement on the terms under which the data is being shared.

The agreement is known as an Information Sharing Agreement, and can be a reference to a standardized agreement (a Standard Information Sharing Agreement or SISA) or a one-off specialized contract.

The base profile is HTTP-based, but any protocol that affords methods for initiating and responding to data transactions, along with metadata (headers) accompanying those interactions could be adapted.

### _Copyright Notice_

&copy; JLINCLabs 2018

### _Table of Contents_

1. [Notation and Conventions](#1-notation-and-conventions)
2. [Definitions](#2-definitions)
3. [Overview](#3-overview)
4. [Method Name](#4-method-name)
5. [Format](#5-format)
6. [Operations](#6-operations)


### _1. Notation and Conventions_

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

### _2. Definitions_

*  JLINC - the [JLINC protocol](https://protocol.jlinc.org/).
*  JSON-LD - the JSON based serialization format defined at [https://www.w3.org/TR/json-ld/](https://www.w3.org/TR/json-ld/).
*  Base64 - the URL-safe variant of Base64 encoding defined in [RFC 4648](https://www.ietf.org/rfc/rfc4648.txt) as base64url without padding.
*  JWT - a JSON Web Token, as defined in RFC 7519.

### _3. Overview_

This specification defines the JLINC methods for creating, resolving and modifying DID documents.  It conforms to the requirements specified in the [DID specification](https://w3c-ccg.github.io/did-spec/) currently published by the W3C Credentials Community Group.

### _4. Method Name_

The namestring that shall identify the JLINC DID method is: jlinc.

A DID that uses this method MUST begin with the following prefix: did:jlinc. Per the DID specification, this string MUST be in lowercase.

### _5. Format_

The JLINC DID has the following ABNF format:

```
jlinc-did = "did:jlinc:" id-string
id-string = 1* idchar
idchar    = ALPHA / DIGIT / "-" / "_"
```

The idchar consists of the characters in the BASE64 UrlSafe character set defined in [RFC 4648](https://www.ietf.org/rfc/rfc4648.txt) as base64url without padding.

Example:  
`did:jlinc:0fil1CNmwie8TevnTJckrvqsk9nyvo8U_3YRLeagAhI`

### _6. Operations_

As a prerequisite the JLINC DID system publishes a master curve25519 public key for the system at the root of the service (`https://did.jlinc.org/`) as well as in other venues. It may change from time to time.

#### 6.1. Register (create)

POST to `/register` with a JSON payload exemplified as follows:

```json
{
  "did": {
    "@context": "https://w3id.org/did/v1",
    "id": "did:jlinc:0fil1CNmwie8TevnTJckrvqsk9nyvo8U_3YRLeagAhI",
    "created": "2018-10-13T17:00:00Z"
  },
  "secret": "yOXRmKr7vZO2kI90Wzce3vrVXRQgIoRc",
  "signature": "xCNUhhxGpdMNCu5H5ym8uspP1qMAzJa5edQBzlskGPIHlDyqJoD6D1BfDMTaGHKQS7kp"
}
```

__The `did` value MUST be the actual DID document__ that the registrant wishes to have recorded. It must be a valid DID document according to the [DID specification](https://w3c-ccg.github.io/did-spec/) and at a minimum it MUST contain:

* the URL for the generic DID context as specified in the [DID specification](https://w3c-ccg.github.io/did-spec/)
* a DID subject with the value `did:jlinc:` followed by an ed25519 detached signing public key in Base64 format
* a `created` timestamp - the system will check the timestamp to be sure it is within reasonable bounds of the current time, allowing for network delays and small server time variations.

__The `secret` is a random 32 byte value__, encoded with the system master public key. This secret must be kept confidential and safe by the registrant, as it is required to perform any further operations on this DID.

__The `signature` is generated__ by first concatenating the encoded secret string, a dot (`.`), and the created timestamp from the DID. A SHA256 value is then generated from the concatenated string, and signed using the private key that can be validated by the public key encoded in the DID subject (`0fil1CNmwie8TevnTJckrvqsk9nyvo8U_3YRLeagAhI` in the example above). Finally the signature is Base64 encoded.

If the DID has already been registered, the system will return an HTTP 409 status, and a JSON body of  
`{"error": "did:jlinc:0fil1CNmwie8TevnTJckrvqsk9nyvo8U_3YRLeagAhI already exists" }`.

If any of the system checks fail, an HTTP 400 status will be returned along with a JSON body containing the error description(s) under an `error` key.

Otherwise, the system will return an HTTP 200 status and a JSON body containing   
the new DID's `id` and a `challenge` string. Example:

```json
{
  "id": "did:jlinc:0fil1CNmwie8TevnTJckrvqsk9nyvo8U_3YRLeagAhI",
  "challenge": "8354eaccf3a8ba6bebfe009e13e4e92956143cb3342afb906d99d28724bd3df9"
}
```

At this point the new DID is not yet active and will not show up in any resolve requests.  
The registrant must now construct a JSON document with the DID `id` in question and a signature over the challenge string with the private key that can be validated by the public key encoded in the DID subject, in Base64 encoding. Example:

```json
{
  "id": "did:jlinc:0fil1CNmwie8TevnTJckrvqsk9nyvo8U_3YRLeagAhI",
  "signature": "BdtJT_d7nTsc_oMrns9W7BNyPIN1uV3UaRv4im6qmVmhxP5rKx9JbFiq6-pkqAbcn"
}
```

A JWT is then constructed with this JSON document as the payload (i.e. "claims set"). The JWT MUST be secured with the SHA256 HMAC method ('HS256'), and the HMAC secret key MUST be the value that was sent encoded in the system's master public key in the initial request.

This JWT must then be POSTed to the `/confirm` endpoint.

If the JWT verifies and the signature contained within also verifies, an HTTP status 201 is returned with a body containing a JSON document with just the `id` value of the newly activated DID.





#### 6.2 Resolve (read)

#### 6.3 Supersede (update)

#### 6.4 Revoke (delete)
