# DID Method Specification

`did:jwk` is a deterministic transformation of a JWK into a DID Document.

## DID Format

```
did-jwk-format   := did:jwk:<base64url-value>
base64url-value  := [A-Za-z0-9_-]+
```

The `base64url-value` is a [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5) encoded [JSON Web Key](https://datatracker.ietf.org/doc/html/rfc7517) (JWK).


## DID Operations

### Create

#### To create the DID:

1. Generate or load a JWK
1. Serialize it into a UTF-8 string
1. Encode that string using base64url
1. Attach the prefix `did:jwk:`

Canonicalization such as [JCS](https://datatracker.ietf.org/doc/html/rfc8785) is not required but may be helpful if the resulting DID isn't going to be stored in its serialized form.

#### To create the DID Document:

The JWK is used to generate the [DID Document](https://www.w3.org/TR/did-core/#dfn-did-documents).  The resulting document will take the form below with the [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5) encoded value described above replacing `${base64url-value}`, and the JSON Web Key structure replacing `${json-web-key}`:
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/jws-2020/v1"
  ],
  "id": "did:jwk:${base64url-value}",
  "verificationMethod": [
    {
      "id": "did:jwk:${base64url-value}#0",
      "type": "JsonWebKey2020",
      "controller": "did:jwk:${base64url-value}",
      "publicKeyJwk": ${json-web-key}
    }
  ],
  "assertionMethod": ["did:jwk:${base64url-value}#0"],
  "authentication": ["did:jwk:${base64url-value}#0"],
  "capabilityInvocation": ["did:jwk:${base64url-value}#0"],
  "capabilityDelegation": ["did:jwk:${base64url-value}#0"],
  "keyAgreement": ["did:jwk:${base64url-value}#0"]
}
```

If the JWK contains a `use` property with the value "sig" then the `keyAgreement` property is not included in the DID Document.  If the `use` value is "enc" then _only_ the `keyAgreement` property is included in the DID Document.

The JWK _should_ have the appropriate `use` value set to match the capabilities of the specified `crv`.  For example, the curve `ed25519` is only valid for "sig" use and `X25519` is only valid for "enc" (see [RFC 8037](https://datatracker.ietf.org/doc/html/rfc8037) and the second example below).

The JWK may contain additional custom properties and values which will be accessible only in the `verificationMethod`.  Any additional properties other than `use` (as documented above) are not referenced or used in the generation of the DID Document.

#### To create the DID URL:

Since `did:jwk` only contains a single key, the DID URL fragment identifier is always a fixed `#0` value.

If the JWK contains a `kid` value it is _not_ used as the reference, `#0` is the only valid value.


### Read

1. Remove the prefix `did:jwk:`
2. Decode the remaining string using [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5)
3. Parse the decoded string as UTF-8 JSON
4. Validate the JWK properties
5. Generate a DID Document using the contained public key material as defined above in 

### Update

Not supported.

### Deactivate

Not supported.

## Examples

### P-256

#### DID
```text
did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9
```

#### DID URL
```text
did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9#0
```

#### DID Document
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/jws-2020/v1"
  ],
  "id": "did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9",
  "verificationMethod": [
    {
      "id": "did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9#0",
      "type": "JsonWebKey2020",
      "controller": "did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9",
      "publicKeyJwk": {
        "crv": "P-256",
        "kty": "EC",
        "x": "acbIQiuMs3i8_uszEjJ2tpTtRM4EU3yz91PH6CdH2V0",
        "y": "_KcyLj9vWMptnmKtm46GqDz8wf74I5LKgrl2GzH3nSE"
      }
    }
  ],
  "assertionMethod": ["did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9#0"],
  "authentication": ["did:jwk:$eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9#0"],
  "capabilityInvocation": ["did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9#0"],
  "capabilityDelegation": ["did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9#0"],
  "keyAgreement": ["did:jwk:eyJjcnYiOiJQLTI1NiIsImt0eSI6IkVDIiwieCI6ImFjYklRaXVNczNpOF91c3pFakoydHBUdFJNNEVVM3l6OTFQSDZDZEgyVjAiLCJ5IjoiX0tjeUxqOXZXTXB0bm1LdG00NkdxRHo4d2Y3NEk1TEtncmwyR3pIM25TRSJ9#0"]
}
```

### X25519

#### DID
```text
did:jwk:eyJrdHkiOiJPS1AiLCJjcnYiOiJYMjU1MTkiLCJ1c2UiOiJlbmMiLCJ4IjoiM3A3YmZYdDl3YlRUVzJIQzdPUTFOei1EUThoYmVHZE5yZngtRkctSUswOCJ9
```

#### DID URL
```text
did:jwk:eyJrdHkiOiJPS1AiLCJjcnYiOiJYMjU1MTkiLCJ1c2UiOiJlbmMiLCJ4IjoiM3A3YmZYdDl3YlRUVzJIQzdPUTFOei1EUThoYmVHZE5yZngtRkctSUswOCJ9#0
```

#### DID Document
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/jws-2020/v1"
  ],
  "id": "did:jwk:eyJrdHkiOiJPS1AiLCJjcnYiOiJYMjU1MTkiLCJ1c2UiOiJlbmMiLCJ4IjoiM3A3YmZYdDl3YlRUVzJIQzdPUTFOei1EUThoYmVHZE5yZngtRkctSUswOCJ9",
  "verificationMethod": [
    {
      "id": "did:jwk:eyJrdHkiOiJPS1AiLCJjcnYiOiJYMjU1MTkiLCJ1c2UiOiJlbmMiLCJ4IjoiM3A3YmZYdDl3YlRUVzJIQzdPUTFOei1EUThoYmVHZE5yZngtRkctSUswOCJ9#0",
      "type": "JsonWebKey2020",
      "controller": "did:jwk:eyJrdHkiOiJPS1AiLCJjcnYiOiJYMjU1MTkiLCJ1c2UiOiJlbmMiLCJ4IjoiM3A3YmZYdDl3YlRUVzJIQzdPUTFOei1EUThoYmVHZE5yZngtRkctSUswOCJ9",
      "publicKeyJwk": {
        "kty":"OKP",
        "crv":"X25519",
        "use":"enc",
        "x":"3p7bfXt9wbTTW2HC7OQ1Nz-DQ8hbeGdNrfx-FG-IK08"
      }
    }
  ],
  "keyAgreement": ["did:jwk:eyJrdHkiOiJPS1AiLCJjcnYiOiJYMjU1MTkiLCJ1c2UiOiJlbmMiLCJ4IjoiM3A3YmZYdDl3YlRUVzJIQzdPUTFOei1EUThoYmVHZE5yZngtRkctSUswOCJ9#0"]
}
```

## Security and Privacy Considerations

Since this JWK method is very similar to the DID Key method, see also [did-key](https://w3c-ccg.github.io/did-method-key/#security-and-privacy-considerations)

### Security

Only JWKs containing public key material may be used, a JWK for a private key must never be used and must be rejected by all implementations when encountered.

Since the key material is part of the identifier there is no support for key rotation, if the key is compromised then the identifier becomes unusable and unrecoverable.

This method does not provide any guarantees of a 1:1 relationship between the identifier and the public key.  The same public key can result in many different identifiers due to variances in how the JWK JSON is serialized.  This is a design choice, implementations should always store the fully serialized `did:jwk:` URI and not the underlying JWK.

There is no provided means of cryptographically verifying possession of the public key material, any such verification must be performed separately by applications using a sufficient challenge-response protocol.

### Privacy

Using the same DID JWK identifier with multiple different entities will enable those entities to correlate the usage to the same subject.
