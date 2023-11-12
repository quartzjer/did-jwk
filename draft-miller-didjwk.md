---
title: "DID Method Specification Using JSON Web Keys"
abbrev: "DIDJWK"
docname: draft-miller-didjwk-00
category: info

ipr: trust200902
area: "General"
workgroup: "Individual Submission"
keyword: ["Decentralized Identifiers", "JSON Web Keys", "DID Method"]

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: J. Miller
    name: Jeremie Miller
    email: jeremie.miller@gmail.com

normative:
  RFC2119:

informative:
  JSON:
  JWK:
  DIDCORE:

--- abstract

This Internet Draft specifies a method for generating Decentralized Identifiers (DIDs) using JSON Web Keys (JWKs). The document introduces the conceptual framework and protocol mechanisms for creating and managing DIDs that are verifiable and can be used within decentralized identity systems. It provides guidance on the structure, method, and processes for generating DIDs with JWKs, ensuring interoperability and security in decentralized identity management. This draft aims to standardize a method that enables users and systems to leverage the benefits of both DIDs and JWKs in a cohesive and integrated manner.

--- middle

# Introduction

The decentralized nature of the Internet has led to the emergence of innovative approaches to identity management, one of which is the concept of Decentralized Identifiers (DIDs). DIDs are a new type of identifier that enable verifiable, self-sovereign digital identities. Unlike traditional identifiers managed by centralized entities, DIDs are fully under the control of the DID subject, thereby enhancing privacy and user autonomy.

The "DID Method Specification Using JSON Web Keys (JWKs)" presents a method for generating and managing DIDs using JSON Web Keys, a standard format for representing cryptographic keys in a JSON object. This method leverages the widespread adoption and interoperability of JWKs to provide a secure and efficient mechanism for DID operations. By integrating DIDs with JWKs, this specification offers a robust framework for decentralized identity management, with a strong emphasis on security, privacy, and ease of use.

The draft outlines the procedures and structures necessary for creating, resolving, updating, and deactivating DIDs within the JWK framework. It discusses the cryptographic underpinnings essential for ensuring the integrity and verifiability of DIDs and delves into the operational aspects of DID management. This approach not only simplifies the handling of digital identities but also aligns with the broader objectives of decentralized systems in terms of scalability, resilience, and user control.

This document aims to contribute to the evolving landscape of digital identity by providing a standard method that can be universally adopted for decentralized identity management. It targets developers, system architects, and stakeholders in the identity space, offering them a comprehensive method to integrate DIDs within their systems using the familiar and widely supported JWK format.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} when, and only when, they appear in all capitals, as shown here.

# DID Method Using JSON Web Keys

## DID Format

This section elaborates on the specific format of Decentralized Identifiers (DIDs) when using the `did:jwk` method. This method represents a deterministic transformation of a JSON Web Key (JWK) into a DID Document, thereby enabling the integration of JWKs into DID infrastructure.

The format for a DID using this method is:

```
did-jwk-format   := did:jwk:<base64url-value>
base64url-value  := [A-Za-z0-9_-]+
```

Here, the `base64url-value` is a base64url encoded representation of a JSON Web Key as defined in [RFC 7517](https://datatracker.ietf.org/doc/html/rfc7517). The encoding process converts the JWK into a format suitable for embedding within a URI, ensuring compatibility with standard web technologies and facilitating ease of use across various platforms.

### Format Specification

- **Prefix**: All DIDs under this method MUST begin with the prefix `did:jwk:`. This prefix is a constant that indicates the specific DID method in use.
- **Base64URL Encoding**: The JSON Web Key MUST be serialized into a UTF-8 string and subsequently encoded using base64url, the URL-safe variant of the standard base64 encoding. This encoding avoids characters that may have special meanings in URIs.
- **Decoding**: To retrieve the original JWK from a DID, the base64url value is decoded, converting it back into the original JSON format.

### Considerations

- **Uniformity**: The `did:jwk` method ensures that every JWK corresponds to a unique and distinct DID, thereby preventing collisions and ambiguity in DID assignments.
- **Versatility**: This format supports various types of cryptographic keys represented in JWK format, providing flexibility in cryptographic practices and key management.
- **Interoperability**: Adhering to standard base64url encoding ensures that DIDs can be easily transmitted, stored, and processed across diverse systems and platforms.

This format specification is integral to the `did:jwk` method, ensuring that DIDs are generated, represented, and managed in a consistent and standardized manner. By leveraging the familiar and widely-adopted JWK format, this method contributes to the broader goal of interoperable and secure digital identity management.

## DID Operations

This section describes the core operations that can be performed using the `did:jwk` method. These operations are fundamental to the management and use of DIDs and include Create, Read, Update, and Deactivate. The description of each operation follows the guidelines established by the DID Core Specification.

### Create

The process of creating a new DID using the `did:jwk` method involves the generation or selection of a JSON Web Key (JWK) and its transformation into a DID. The steps are as follows:

1. **Generate or Load a JWK**: 
   - If no existing JWK is to be used, generate a new JWK following the specifications outlined in [RFC 7517](https://datatracker.ietf.org/doc/html/rfc7517). 
   - For existing keys, load the JWK from its storage or representation format.

2. **Serialize the JWK**: 
   - Convert the JWK into a JSON format as per RFC 7517.
   - Ensure that the JSON string is encoded in UTF-8 to maintain consistency and compatibility with international character sets.

3. **Encode the JSON String**: 
   - Use base64url encoding, a URL-safe variant of base64, to encode the UTF-8 JSON string. This encoded string forms the `base64url-value` of the DID.

4. **Construct the DID**: 
   - Prefix the `base64url-value` with `did:jwk:` to form the complete DID. This DID is now ready for use in various applications and systems.

### Read

The Read operation is crucial for resolving a `did:jwk` DID to its corresponding DID Document, which contains public key and other relevant metadata.

1. **DID Resolution**: 
   - Extract the `base64url-value` from the DID.
   - Decode this value using base64url decoding to retrieve the original JSON string representing the JWK.

2. **Construct the DID Document**: 
   - Convert the JWK into a standard DID Document as per the DID Core Specification.
   - The DID Document should include essential metadata like `@context`, `id`, and the `verificationMethod` fields, consistent with the standard DID Document structure.

Example DID Document structure:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/jws-2020/v1"
  ],
  "id": "did:jwk:{base64url-value}",
  "verificationMethod": [
    {
      "id": "did:jwk:{base64url-value}#0",
      "type": "JsonWebKey2020",
      "controller": "did:jwk:{base64url-value}",
      "publicKeyJwk": {decoded-json-web-key}
    }
  ],
  ...
}
```

### Immutability of `did:jwk` DIDs

DIDs generated using the `did:jwk` method are immutable. This means:

- **No Updates**: Once a DID is created, it cannot be modified. If a new key is required, a new DID must be generated.
- **No Deactivation**: These DIDs do not support deactivation. They remain valid as long as the underlying JWK is considered secure and relevant.

This immutability is a key feature of the `did:jwk` method, ensuring the reliability and trustworthiness of the DIDs generated. It simplifies the management of these DIDs, as there are no mechanisms needed for updating or deactivating them.

## Examples

### Example 1: P-256 Key

Example showing a DID and DID Document for a P-256 key.

### Example 2: X25519 Key

Example showing a DID and DID Document for an X25519 key.

# Security and Privacy Considerations

## Key Security

The security of the `did:jwk` method is inherently tied to the security of the underlying JSON Web Key. It is essential to ensure the secure generation, storage, and handling of JWKs. Loss, theft, or unauthorized disclosure of the private component of a JWK can lead to identity theft, impersonation, and other security breaches.

### Recommendations:

- Implement robust cryptographic practices for key generation, including using sufficient key lengths and secure random number generation.
- Protect private keys with appropriate access controls and secure storage solutions.
- Regularly audit and update cryptographic practices to mitigate evolving threats and vulnerabilities.

## Immutability of DIDs

The `did:jwk` method creates immutable DIDs. Once a DID is generated, it cannot be altered or revoked, which has both security and privacy implications.

### Implications:

- Loss or compromise of the corresponding private key renders the DID permanently unusable for authentication or signing purposes.
- Immutability ensures that a DID will always resolve to the same DID Document, providing consistency and reliability.

## Privacy Considerations

The use of a single DID across multiple platforms or contexts can lead to correlation and potential privacy risks.

### Best Practices:

- Consider using different DIDs for different contexts to reduce the risk of correlation and tracking.
- Be aware of the public nature of DIDs and DID Documents. Avoid embedding sensitive or personally identifiable information within a DID Document.

## Public Key Exposure

DIDs are based on public keys that are openly exposed in DID Documents. This exposure does not compromise the security of the key itself but may have privacy implications.

### Considerations:

- Evaluate the trade-off between the transparency provided by public key exposure and the potential for misuse or privacy invasion.
- Implement additional layers of privacy protection when necessary, such as using pseudonymous DIDs for sensitive applications.

## Key Rotation and Management

The immutable nature of the `did:jwk` method does not support key rotation, which is a common practice in key management to mitigate the risk of long-term key exposure and compromise.

### Mitigation Strategies:

- Develop and implement robust initial key management practices, recognizing the long-term nature of the key associated with a DID.
- In case of key compromise, a new DID must be generated, and stakeholders must be informed to transition to the new DID.

In conclusion, while the `did:jwk` method provides a robust framework for decentralized identity management using DIDs and JWKs, careful consideration and implementation of security and privacy best practices are essential to mitigate potential risks.

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO: Acknowledgments.

