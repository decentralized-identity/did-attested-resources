## Abstract

A **DID Attested Resource** is a verifiable JSON envelope, identified by a [[ref: DID URL]], that carries: (1) an arbitrary JSON `content` object (the “resource”), (2) minimal [[ref: metadata]] used for dereferencing, (3) optional [[ref: links]] to related material, and (4) a [[spec:DATA-INTEGRITY]] proof over the whole envelope.  

The identifier of a DID Attested Resource is bound to its `content` by embedding a deterministic digest in the [[ref: DID URL]], allowing resolvers to independently verify that the dereferenced resource has not been altered. By associating resources with a DID, DID Attested Resources inherit the trust model of the DID and its Controller: the DID establishes control, while the DID Attested Resource proof binds that control to the specific resource.  

This specification defines the data model, identifier construction, digest calculation, proof requirements, creation/publishing steps, and resolver behavior for DID Attested Resources. It is independent of any particular resource type (e.g., AnonCreds objects) and any particular DID Method. Specifications (such as [AnonCreds Objects as DID Attested Resources]) may impose additional constraints outside this document.

[AnonCreds Objects as DID Attested Resources]: https://identity.foundation/didwebvh/anoncreds-method

### Design Goals (Non-Normative)

- **DID Anchoring**: Ensure every resource is anchored to a DID and inherits its trust model.  
- **Self-verifying identifier**: Bind the identifier to the content via a deterministic digest in the [[ref: DID URL]].  
- **Method-agnostic**: Work with any DID Method; specific methods define how [[ref: DID URL]]s resolve to transport addresses.  
- **Portable envelope**: Keep the verifiable wrapper independent of specific resource schemas.  
