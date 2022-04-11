## DID Method Specification

`did:jwk` is a deterministic transformation of a JWK into a DID Document.

### DID Format

```
did-jwk-format   := did:jwk:<base64url-value>
base64url-value  := [A-Za-z0-9_-]+
```

The `base64url-value` is a [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5) encoded [JSON Web Key](https://datatracker.ietf.org/doc/html/rfc7517) (JWK).


### DID Operations

#### Create

1. Generate a JWK
2. Serialize it into a UTF-8 string
3. Encode that string using base64url
4. Attach the prefix `did:jwk:`

The JWK is also used to generate the [DID Document](https://www.w3.org/TR/did-core/#dfn-did-documents).  The resulting document will take the form below with the Base64 URL encoded value described above replacing `${base64url-value}`, and the JSON Web Key structure replacing `${json-web-key}` :
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

#### Read

1. Remove the prefix `did:jwk:`
2. Decode the remaining string using base64url
3. Parse the decoded string as UTF-8 JSON
4. Validate the JWK properties
5. Generate a DID Document using the contained public key material

#### Update

Not supported.

#### Deactivate

Not supported.


### Security and Privacy Considerations

Since this JWK method is very similar to the DID Key method, see also [did-key](https://w3c-ccg.github.io/did-method-key/#security-and-privacy-considerations)

#### Security

Only JWKs containing public key material may be used, a JWK for a private key must never be used and must be rejected by all implementations when encountered.

Since the key material is part of the identifier there is no support for key rotation, if the key is compromised then the identifier becomes unusable and unrecoverable.

This method does not provide any guarantees of a 1:1 relationship between the identifier and the public key.  The same public key can result in many different identifiers due to variances in how the JWK JSON is serialized.  This is a design choice, implementations should always store the fully serialized `did:jwk:` URI and not the underlying JWK.

There is no provided means of cryptographically verifying possession of the public key material, any such verification must be performed separately by applications using a sufficient challenge-response protocol.

#### Privacy

Using the same DID JWK identifier with multiple different entities will enable those entities to correlate the usage to the same subject.
