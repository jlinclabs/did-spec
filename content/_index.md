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

### _3. Overview_

This specification defines the JLINC methods for creating, resolving and modifying DID documents.  It conforms to the requirements specified in the [DID specification](https://w3c-ccg.github.io/did-spec/) currently published by the W3C Credentials Community Group.

### _4. Method Name_

The namestring that shall identify the JLINC DID method is: jlinc.

A DID that uses this method MUST begin with the following prefix: did:jlinc. Per the DID specification, this string MUST be in lowercase.

### _5. Format_

The JLINC DID has the following format:

```
jlinc-did   = "did:jlinc:" id-string
id-string = 1* idchar
idchar    = ALPHA / DIGIT / "-" / "_"
```

idchar consists of all the characters in the BASE64 UrlSafe character set defined in [RFC 4648](https://www.ietf.org/rfc/rfc4648.txt) as base64url without padding.

Example:  
`did:jlinc:0fil1CNmwie8TevnTJckrvqsk9nyvo8U_3YRLeagAhI`

### _6. Operations_
