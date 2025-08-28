## DID Attested Resource Specification

### Data Model

A DID Attested Resource is a JSON object with the following attributes:

```json
{
  "@context": [
    "https://identity.foundation/did-attested-resources/context/{specVersion}",
    "https://w3id.org/security/data-integrity/v2"
  ],
  "type": ["AttestedResource"],
  "id": "did:<method>:<method-specific-id>/<path...>/{digestMultibase}",
  "version": "{specVersion}",
  "content": { /* arbitrary JSON resource */ },
  "metadata": {
    "resourceId": "{digestMultibase}",
    "resourceType": "",
    "resourceName": "",
    "resourceVersion": ""
  },
  "links": [
    {
      "id": "did:<...> or https://<...>",
      "mediaType": "",
      "resourceType": "",
      "timestamp": "",
      "digestMultibase": "{digestMultibase}"
    }
  ],
  "proof": {
    "type": "DataIntegrityProof",
    "cryptosuite": "eddsa-jcs-2022",
    "verificationMethod": "did:<method>:<...>#<key-id>",
    "proofPurpose": "assertionMethod",
    "proofValue": "..."
  }
}
```

**Requirements**

- `@context` **MUST** be an array containing `https://identity.foundation/did-attested-resources/context/v0.1` as its first element. It **MUST** also include the VC Data Integrity context URL `https://w3id.org/security/data-integrity/v2` as the second element.
- `type` **MUST** be an array including the value `"AttestedResource"`.  
- `id` **MUST** be a [[ref: DID URL]] under the control of the [[ref: DID Controller]].  
  The rightmost path segment of the `id` **MUST** begin with the `digestMultibase` of the `content`.  
  An optional file extension (e.g., `.json`) **MAY** follow the digest in the path segment.  
- `version` **MUST** be a string indicating which spec version was used when creating the resource.
- `content` **MUST** be a JSON object; its schema is profile-specific.
- `metadata.resourceId` **MUST** equal the computed `digestMultibase` of content.
- `metadata.resourceType` **SHOULD** identify the resource type expected in the calling profile.
- `links.id` entries **MAY** be other [[ref: DID URL]]s, enabling chains of DID Attested Resources.
- `proof.verificationMethod` **MUST** reference a verification method in the Controller’s [[ref: DID Document]], thereby linking the proof to the DID.
- `proof.cryptosuite` **MUST** have the value `"eddsa-jcs-2022"`

### Identifier and Addressing

The identifier of a DID Attested Resource is always a [[ref: DID URL]].

Resolution from a [[ref: DID URL]] to a retrievable transport URL is DID Method–specific.

A DID Document `service` entry **MAY** advertise the base URI for DID Attested Resources.

Thus, DID Attested Resources combine the self-verifying content hash with the trust anchoring of a DID.

### Calculating the `digestMultibase`

Publishers and Resolvers **MUST** compute `digestMultibase` for the content as:

- `digestMultibase = multibase( multihash( jcs(content), 'sha256' ), 'b58btc' )`

Where:

- `multibase` is defined by the [[spec:multibase]] specification.
- `multihash` is defined by the [[spec:multihash]] specification.
- `jcs` is defined by the JSON Canonicalization Scheme [[spec:RFC8785]] specification.
- `sha256` is the hash algorithm used, as defined in [[spec:6234]] specification.
- `b58btc` is the Base Bitcoin-58 algorithm as defined in the [[spec:base58btc]] specification.

The calculated `digestMultibase` **MUST** matches both the `metadata.resourceId` and the final path segment of the [[ref: DID URL]].

The algorithms listed above to calculate the `digestMultibase`, including `multibase`, `multihash`, `jcs`, `sha256` and `b58btc` **MUST** be be as defined above. Future versions of this specification, identified by a metadata `version` attribute, could use other algorithms.

### Data Integrity Proof

A DID Attested Resource **MUST** include a Data Integrity proof in the `proof`JSON item.

`proof.verificationMethod` **MUST** be a key in the [[ref: DID URL]]'s DID Document, ensuring that only the [[ref: DID Controller]] can publish valid DID Attested Resources under that DID.

### Creation and Publishing (Publisher Behavior)

To create a DID Attested Resource:

- Compute the `digestMultibase` as defined [in this specification](#calculating-the-digestmultibase).
- Define the DID Attested Resource envelope with `id` as a [[ref: DID URL]] whose final path segment is the `digestMultibase`.
- Attach a [[spec:DATA-INTEGRITY]] proof using a verification method from the Controller’s DID Document.
- Publish at a location resolvable from that DID (via DID Method rules or a DID Document `service`).

### Resolving DID Attested Resources (Resolver Behavior)

To resolve a DID Attested Resource:

- Resolve the DID from the [[ref: DID URL]], and validate it per the DID Method.
- Map the [[ref: DID URL]] to a transport URL (via method rules or service endpoints).
- Fetch the DID Attested Resource JSON.
- Verify the identifier, `digestMultibase`, and metadata consistency.
- Verify the proof using the DID Controller’s DID Document.
- Return the validated content (and envelope if needed).

### Links

The `links` JSON attribute is a possibly empty array. Its items **MUST** include:

- `type`: a profile-defined relation type.
- `id`: a [[ref: DID URL]] or URI to the related item.

Its items **MAY** include:

- `timestamp`: A timestamp that **MUST** be a [XMLSCHEMA11-2](https://www.w3.org/TR/xmlschema11-2/#dateTimeStamp) `dateTimeStamp` string value representing the date and time associated with the creation of the linked resource.
- `mediaType`: A valid media type as listed in the [IANA Media Types](https://www.iana.org/assignments/media-types/media-types.xhtml) registry.
- `digestMultibase`: The `digestMultibase` of the resource. This **MAY** be used if the linked resource is not itself a DID Attested Resource.

[[ref: Profiles]] **MAY** define additional fields such as `timestamp` or `digestMultibase`.

Resolvers **MAY** use `links` to discover related DID Attested Resources (e.g., updated versions). For example, in the [spec:DIDWEBVH-ANONCREDS] specification, `links` in AnonCreds `RevRegDef` DID Attested Resources list the associated `RevRegEntry` DID Attested Resources, each of which defines the state of the AnonCreds Revocation Registry at a given `timestamp`.

Since the `links` item is outside of the `context` (the attested resource), the `digestMultibase` for the DID Attested Resource will not change if the `links` item is changed. However, the `links` items is covered by the [[spec:DATA-INTEGRITY]] proof.

### JSON-LD Context

The DID Attested Resources JSON-LD context is as follows:

```json
{
  "@context": {
    "@protected": true,
    "id": "@id",
    "type": "@type",

    "digestMultibase": {
      "@id": "https://w3id.org/security#digestMultibase",
      "@type": "https://w3id.org/security#multibase"
    },

    "AttestedResource": {
      "@id": "https://example.com/attested-resource#AttestedResource",
      "@protected": true,
      "@context": {
        "content": {
          "@id": "https://example.com/attested-resource#content",
          "@type": "@json"
        },
        "metadata": {
          "@id": "https://example.com/attested-resource#metadata",
          "@type": "@json"
        },
        "links": {
          "@id": "https://example.com/attested-resource#links",
          "@type": "@json"
        }
      }
    }
  }
}
```

### Interoperability Notes (Non-Normative)

**Profiles**: Communities can define DID Attested Resource [[ref: profiles]] for specific ecosystems (e.g., AnonCreds). Such profiles define the metadata `type` values, and the associated resources listed in the `links` array of a given DID Attested Resource type.

### Security and Privacy Considerations

TO BE ADDED
