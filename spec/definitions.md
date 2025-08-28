## Definitions

[[def: DID Controller, DID Controller's, DID Controllers]]

~ The entity that controls (create, updates, deletes) a given DID, as defined
in the [[spec:DID-CORE]].

[[def: DID Document, DIDDoc]]

~ The JSON document returned from resolved as part of the process of resolving a DID URL.

[[def: DID URL, DID URLs, DID URL's]]

~ A DID URL as defined in the [[spec:DID-RESOLUTION]] specification. A DID URL is resolved by first resolving the DID, and then the specified resource.

[[def: version]]

~ A string indicating the version of the specification which was used when creating the attested resource.

[[def: content]]

~ Arbitrary json object which is immutable and bound to the `ressourceId`.

[[def: metadata]]

~ Additional data provided about the DID Attested Resource, including at least the `resourceType`, and `resourceId`.

[[def: Profiles, profiles, Profile, profile]]

~ Semantic information about one or more related DID Attested Resources defined by a community for use within an ecosystem.

[[def: links, Links]]

~ A (possibly empty) list of URLs related to a given DID Attested Resource within the Attested Resource file. The URLs may themselves be DID Attested Resources. The `links` are not part of the resource, and so changing the list does not alter the `digestMultibase` of the DID Attested Resource. The `links` attribute is covered by the Data Integrity `proof` that is included in the DID Attested Resource.