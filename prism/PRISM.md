# PRISM v1.0 — Protocol for Reproducible Interchange of Statements & Meanings
SPDX-FileCopyrightText: © 2025–present Victor M. Barrientos <firmw.guy@gmail.com>
SPDX-License-Identifier: CC-BY-SA-4.0
**Status:** Draft for implementation  
**Last updated:** 2025-12-26  
**Audience:** implementers of semantic packs, claim/evidence publishers, validators, and downstream stores/indexers (e.g., fact graph engines)

PRISM defines:
- **canonical, content-addressed artifacts** for meanings, statements (Claims), and evidence,
- a **machine-checkable definition discipline** for meanings (grounded definitional graphs),
- an **evidence chain model** robust to link rot and adversarial media,
- **verifiability levels** (PVL) that classify what can be mechanically verified about an artifact and its dependency closure.

PRISM does **not** define:
- governance/legitimacy/authority (handled by governance layers such as “Weave”),
- storage/indexing/query/discovery execution (handled by engines such as “NEXUS”),
- network transport (how artifacts move peer-to-peer), beyond import/export expectations.

> **Non-goal (important):** PRISM does not claim to decide “truth.”  
> PRISM classifies the **verifiability** and **grounding** of claims and meanings.

---

## Table of contents
1. Conformance language  
2. Core concepts  
3. Identifiers and identity rules  
4. Canonicalization, hashing, and signing  
5. PRISM Artifact Envelope  
6. PrismLex Packs and Meaning Definitions  
7. Claim Model  
8. Evidence Model  
9. Templates and PVL-3 Validation  
10. PRISM Verifiability Levels (PVL)  
11. Discovery overlays (non-normative)  
12. Compatibility, projections, and extension points  
13. Security and privacy considerations  
14. Conformance checklist  

Appendices:
- Appendix A — Recommended core packs (non-normative)
- Appendix B — Design invariants (non-normative)
- Appendix C — PrismTag Encoding Profile (PTEP) (normative optional)
- Appendix D — PrismSigil Rendering Profile (PSRP) (normative optional)
- Appendix E — PMF Projection Notes (PrismTags + PrismRoles) (informative)
- Appendix F — Encoding, normalization, and span rules (normative optional profile)
- Appendix G — Prism Social Signals (PSS) Profile (normative optional)
- Appendix H — Commitment Log / Transparency Profile (normative optional)

---

## 1. Conformance language
The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document are to be interpreted as described in RFC 2119.

When this spec says “validator,” it means a deterministic program that checks PRISM artifacts against the rules for a target PVL.

---

## 2. Core concepts

### 2.1 Artifact
An **artifact** is the canonical unit of PRISM interchange:
- an envelope with metadata + a typed body,
- content-addressed by a cryptographic hash over canonical bytes,
- optionally signed.

Artifacts are immutable: if any field changes, the artifact hash changes.

### 2.2 Meaning (semantic identity)
A **meaning** is a community-defined, globally referable concept represented by a PrismLex entry.

PRISM distinguishes:
- **meaning identity** (stable): `(pack_release_hash, entry_uid)`
- **encodings** (optional): compact tags (e.g., PrismTags) that *resolve to* meaning identity.

### 2.3 Claim
A **Claim** is an atomic assertion about one or more instances using a predicate meaning. Claims can be materialized as edges in a fact graph, but PRISM treats them as first-class artifacts.

> PRISM uses “Claim” in the core protocol. “Fact” is a *view* (e.g., “claim adopted as a fact by a governance context”), not a PRISM primitive.

### 2.4 Evidence chain
An **evidence chain** is a structured set of references that connects a claim to supporting material:
- **Substrate**: the bytes (or stable hash of bytes),
- **Selector**: what portion is relevant,
- **Derivation(s)**: explicit transformations (e.g., OCR, transcript, parsing),
- **Support assertion**: what the chain is asserted to support and under what mode.

A URL is not evidence; it is at most a **locator**.

### 2.5 PVL — PRISM Verifiability Levels
**PVL (0–3)** classifies what a validator can mechanically verify about an artifact:
- structure,
- meaning resolution,
- definition grounding,
- evidence chain integrity and reproducibility,
- deterministic template validation (at PVL-3).

PVL is **not** a truth rating.

### 2.6 Discovery overlays
Discovery overlays are optional, non-normative metadata intended to help search/index/UI. Removing overlays MUST NOT change meaning, validity, or identity.

### 2.7 Commitment logs (optional)
A **commitment log** is an append-only transparency structure (Merkle log) that allows third parties to verify a publisher did not rewrite their publication history.

Commitment logs are OPTIONAL in PRISM, but can be required by downstream policy.

---

## 3. Identifiers and identity rules

### 3.1 Hash identifiers
Unless otherwise specified, all hashes use **SHA-256**.

PRISM represents hashes as:
- `sha256:<lowercase-hex>`

Example:
- `sha256:9f2a...`

Validators MUST reject other hash algorithm labels unless an extension profile is explicitly enabled.

### 3.2 PackReleaseHash
A **PackRelease** is an artifact; its artifact hash is the **pack release hash** and serves as the immutable identity of that release.

### 3.3 EntryUID
**EntryUID** is a globally unique identifier for a meaning entry *within a pack release*.

Rules:
- `entry_uid` MUST be unique within a pack release.
- `entry_uid` SHOULD be stable across pack versions if the meaning is unchanged.
- If a meaning changes materially, publishers SHOULD create a new entry_uid and deprecate the old entry rather than mutate it.

### 3.4 PackRef
A **PackRef** names a pack and pins it to a release hash:

```json
{
  "pack_id": "roles-core",
  "release": { "hash": "sha256:..." }
}
```

`pack_id` is human-friendly; identity comes from `release.hash`.

### 3.5 MeaningRef
A **MeaningRef** is the canonical pointer to a meaning:

```json
{
  "meaning_ref": {
    "pack": { "pack_id": "roles-core", "release": { "hash": "sha256:..." } },
    "entry_uid": "uid:role:subject"
  }
}
```

At PVL-2+, MeaningRefs MUST resolve to a locally available, hash-verified PackRelease.

### 3.6 Instance identifiers (non-meaning)
Instances (“beings,” entities, nodes) are not meanings.

PRISM defines:
- `instance_id`: an opaque string with a namespace prefix recommended.

Example:
- `inst:uuid:550e8400-e29b-41d4-a716-446655440000`
- `inst:did:did:key:z6M...`
- `inst:hash:sha256:...` (content-addressed instance identity, if a system chooses)

PRISM does not require global uniqueness for instance IDs, but any system that federates MUST define an instance namespace strategy.

---

## 4. Canonicalization, hashing, and signing

### 4.1 Canonical JSON
PRISM artifact hashes MUST be computed from a **canonical JSON** representation.

Normative baseline:
- Canonicalize JSON using the JSON Canonicalization Scheme (JCS, RFC 8785), or an equivalent procedure that produces identical bytes across implementations.

Additional requirements:
- UTF-8 encoding (no BOM).
- No insignificant whitespace.
- Object keys sorted lexicographically.
- Numbers MUST be represented canonically (no NaN/Infinity).

### 4.2 Artifact hash
The **artifact hash** is computed over the canonical bytes of the envelope with signatures removed.

Define `unsigned_envelope` as the artifact envelope with:
- `signatures` removed entirely (or replaced with an empty array).

Then:
- `artifact_hash = "sha256:" + hex( SHA256( canonical_json(unsigned_envelope) ) )`

### 4.3 Signatures
Signatures are OPTIONAL at PVL-0/1 and RECOMMENDED at PVL-2+. Some ecosystems may REQUIRE signatures by policy.

Signature objects:

```json
{
  "alg": "ed25519",
  "key": "did:key:z6M...",
  "sig": "base64url...",
  "signed_hash": "sha256:...",
  "created_at": "2025-12-26T00:00:00Z"
}
```

Rules:
- `signed_hash` MUST equal the artifact hash computed per §4.2.
- `sig` MUST be the signature over the raw bytes of the artifact hash string in UTF-8, unless a profile specifies otherwise.
- Validators MUST ignore unknown `alg` values unless an extension profile is enabled.

---

## 5. PRISM Artifact Envelope (normative)

### 5.1 Envelope schema
Every PRISM artifact MUST be a JSON object with at least:

```json
{
  "prism_version": "1.0",
  "artifact_type": "Claim",
  "artifact_version": "1.0",

  "created_at": "2025-12-26T00:00:00Z",

  "pvl": 2,

  "context_ref": null,

  "visibility": "public",

  "dependencies": {
    "packs": [
      { "pack_id": "predicates-core", "release": { "hash": "sha256:..." } }
    ],
    "artifacts": [ "sha256:..." ]
  },

  "body": { },

  "discovery_hints": null,

  "signatures": [
    { "alg": "ed25519", "key": "did:key:...", "sig": "base64url...", "signed_hash": "sha256:...", "created_at": "..." }
  ]
}
```

### 5.2 Field semantics
- `prism_version`: protocol major version. MUST be `"1.0"` for this spec.
- `artifact_type`: type name selecting the body schema. MUST be a string.
- `artifact_version`: version for the given artifact type schema. MUST be `"1.0"` unless the type’s registry says otherwise.
- `created_at`: ISO 8601 timestamp in UTC (`...Z`). MUST be present.
- `pvl`: declared target PVL (0–3). MUST be present.
- `context_ref`: OPTIONAL opaque pointer to an external governance/context identifier.
- `visibility`: OPTIONAL. Recommended values:
  - `public | federated | members_only | restricted | local_only`
- `dependencies`: OPTIONAL but RECOMMENDED at PVL-2+. See §5.3.
- `body`: REQUIRED. JSON object whose schema depends on `artifact_type`.
- `discovery_hints`: OPTIONAL non-normative object (§11).
- `signatures`: OPTIONAL array of signature objects (§4.3).

### 5.3 Dependencies
Dependencies allow validators and stores to fetch/verify closure.

`dependencies.packs` MAY list PackRefs required to interpret meanings used in the artifact.
`dependencies.artifacts` MAY list artifact hashes required to interpret the artifact (e.g., evidence substrates, derivations, referenced claims).

Rules:
- At PVL-2+, a validator MUST be able to compute the artifact’s PVL validity using only:
  - the artifact bytes,
  - the referenced dependency closure (packs + artifacts),
  - and enabled profiles.
- Missing required dependencies at PVL-2+ causes PVL-2 validation failure (see §10).

### 5.4 context_ref is opaque
PRISM validates only that `context_ref` is well-formed as a string or null.
PRISM MUST NOT interpret authority, legitimacy, membership rules, or policy semantics.

---

## 6. PrismLex Packs and Meaning Definitions (normative)

### 6.1 PackRelease artifact_type
`artifact_type = "PackRelease"`

PackRelease body schema:

```json
{
  "pack": {
    "pack_id": "predicates-core",
    "version": "1.0.0",
    "created_at": "2025-12-01T00:00:00Z",
    "license": { "spdx": "CC-BY-4.0", "text": null },
    "description": { "text": "Core predicates.", "lang": "en" },

    "parents": [
      { "pack_id": "predicates-core", "release": { "hash": "sha256:..." } }
    ],

    "lang_default": "en"
  },

  "entries": [
    {
      "entry_uid": "uid:pred:located_in",
      "kind": "PRIMITIVE",
      "labels": [ { "lang": "en", "text": "located in", "preferred": true } ],
      "gloss": [ { "lang": "en", "text": "Subject is located within object." } ],

      "compact_id": 12345,

      "defaults": {
        "type": "RELATIONAL",
        "assert_true": ["_relational"],
        "assert_false": []
      },

      "canonical_definition": null,
      "presentation_definition": null,

      "relations": [
        { "predicate": { "meaning_ref": { "pack": { "pack_id": "relations-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:is_a" } },
          "object": { "meaning_ref": { "pack": { "pack_id": "predicates-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:pred:binary_predicate" } }
        }
      ],

      "metadata": {
        "deprecated": false,
        "replaced_by": null,
        "created_in_version": "1.0.0"
      }
    }
  ]
}
```

Rules:
- `pack.pack_id` is human-friendly and MAY collide across communities; identity comes from the artifact hash.
- `pack.parents` defines lineage; it does not grant authority.
- A PackRelease MUST be immutable once published.

### 6.2 Entry kinds
Each `entries[]` element MUST declare `kind`:

- `PRIMITIVE`: a root concept; no definitional dependencies.
- `DEFINED`: a meaning with a machine-checkable definition graph (PDG).
- `ALIAS`: points to another meaning (no definition).
- `TEMPLATE`: schema/pattern used for PVL-3 validation.
- `OPERATOR`: logical/structural operator meaning (e.g., AND, OR, NOT, EXISTS).

### 6.3 Compact IDs (optional)
`compact_id` MAY be included for efficiency.

Rules:
- Compact IDs MUST NOT be required for meaning identity at PVL-2+.
- Compact IDs MUST NOT be reused for different meanings within the same pack lineage.

### 6.4 Canonical definition graph (PDG)
For `kind == "DEFINED"`, `canonical_definition` is REQUIRED and MUST be a **Prism Definition Graph (PDG)**.
For `kind == "PRIMITIVE"`, `canonical_definition` MUST be null.
For `kind == "ALIAS"`, `canonical_definition` MUST be null.

PDG schema:

```json
{
  "vars": [
    { "name": "$x", "primary": true }
  ],
  "nodes": [
    { "id": "n1", "node_kind": "VAR", "var": "$x" },
    { "id": "n2", "node_kind": "PRED", "meaning": { "meaning_ref": { "pack": { "pack_id": "predicates-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:pred:located_in" } } },
    { "id": "n3", "node_kind": "CONST", "value": { "meaning_ref": { "pack": { "pack_id": "constants-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:const:spacetime" } } }
  ],
  "edges": [
    { "from": "n2", "role": { "meaning_ref": { "pack": { "pack_id": "roles-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:role:subject" } }, "to": "n1" },
    { "from": "n2", "role": { "meaning_ref": { "pack": { "pack_id": "roles-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:role:object" } }, "to": "n3" }
  ],
  "root": "n2",
  "def_mode": "EQUIV"
}
```

#### 6.4.1 PDG node kinds (minimum)
A conforming implementation MUST support these node kinds:
- `VAR`: a variable reference (by var name).
- `CONST`: a constant meaning or literal (see §6.4.3).
- `PRED`: a predicate meaning.
- `OP`: an operator meaning (logical/compositional).
- `EXPR`: an expression node (typed wrapper when needed).
- `PROP`: a proposition node (typed wrapper when needed).

Extensions MAY add additional node kinds but MUST NOT change the semantics of the minimum set.

#### 6.4.2 PDG def_mode (minimum)
A conforming implementation MUST support:
- `EQUIV`: the entry is equivalent to the proposition at `root`.
- `ISA+CONSTRAINTS`: the entry asserts an `IS_A` relation plus constraints represented by `root`.

#### 6.4.3 Allowed references in PDG
All meanings referenced inside a PDG MUST be represented as MeaningRefs.

At PVL-2+, all such MeaningRefs MUST resolve to verified packs, or the PackRelease is invalid at PVL-2.

Literals inside PDG:
- A CONST node MAY use a literal value:
  ```json
  { "id": "n7", "node_kind": "CONST", "value": { "literal": { "datatype": "string", "value": "foo" } } }
  ```
- Literal datatypes SHOULD be formalized via a datatypes pack at PVL-2+.

### 6.5 Alias entries
For `kind == "ALIAS"`, an entry MUST include:

```json
"alias_of": { "meaning_ref": { "pack": { "pack_id": "...", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:..." } }
```

Rules:
- Alias chains MUST be acyclic.
- Validators MUST resolve aliases before dependency extraction (§6.7).

### 6.6 Template entries
For `kind == "TEMPLATE"`, `canonical_definition` MUST contain a **template pattern** instead of PDG.

Template pattern schema (minimum):

```json
{
  "template": {
    "template_id": "T:CLAIM/LOCATED_IN",
    "variables": [
      { "name": "$s", "role": "subject" },
      { "name": "$o", "role": "object" }
    ],
    "requirements": {
      "predicate": { "meaning_ref": { "pack": { "pack_id": "predicates-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:pred:located_in" } },
      "subject_type": null,
      "object_type": null
    },
    "constraints": [
      { "kind": "cardinality", "field": "evidence", "min": 1 }
    ]
  }
}
```

Template semantics are enforced at PVL-3 (see §9 and §10.4).

### 6.7 Definitional dependencies (normative)
For an entry E, define `Deps(E)` as the set of MeaningRefs appearing in E’s:
- `canonical_definition` PDG, excluding variables and non-meaning structural markers,
- and excluding alias indirections (aliases are resolved first).

Dependencies include predicates, constants, operators—anything that is a MeaningRef.

### 6.8 Definitional dependency graph, acyclicity, rootedness (normative)
Build a directed graph G where:
- vertices are entries in this PackRelease (and imported entries needed to validate),
- edge `E → D` exists if `D ∈ Deps(E)` and D is not a PRIMITIVE.

Rules:
- PackRelease validity at PVL-2+ requires G to be a DAG.
- “Rootedness” requires that every dependency chain terminates in PRIMITIVE entries (or entries with no canonical_definition dependencies).
- Missing references break rootedness and fail PVL-2+ validation.

Validators MUST implement cycle detection via DFS or topological sort.

---

## 7. Claim Model (normative)

### 7.1 Claim artifact_type
`artifact_type = "Claim"`

Claim body schema (minimum):

```json
{
  "claim": {
    "subject": { "instance_id": "inst:uuid:..." },

    "predicate": { "meaning_ref": { "pack": { "pack_id": "predicates-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:pred:located_in" } },

    "object": { "instance_id": "inst:uuid:..." },

    "scope": {
      "as_of": "2025-12-26T00:00:00Z",
      "valid_time": { "start": null, "end": null },
      "place": null,
      "context_ref": null
    },

    "qualifiers": [
      {
        "predicate": { "meaning_ref": { "pack": { "pack_id": "qualifiers-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:qual:confidence" } },
        "object": { "literal": { "datatype": "number", "value": 0.7 } }
      }
    ],

    "evidence": [
      {
        "support_mode": "DIRECT_QUOTE | MEASUREMENT | DERIVED | ATTESTATION | OTHER",
        "chain": {
          "substrate": { "artifact_hash": "sha256:..." },
          "selector": null,
          "derivations": [ { "artifact_hash": "sha256:..." } ]
        },
        "notes": { "text": "Optional human note.", "lang": "en" }
      }
    ],

    "typed_references": [
      {
        "ref_role": { "meaning_ref": { "pack": { "pack_id": "refs-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:ref:incorporates_by_reference" } },
        "target": { "artifact_hash": "sha256:..." }
      }
    ]
  }
}
```

### 7.2 Subject / object value forms
`subject` and `object` MUST be one of:
- `{ "instance_id": "..." }`
- `{ "meaning_ref": { ... } }` (for claims about meanings)
- `{ "literal": { "datatype": "...", "value": ... } }` (for literal-valued objects)

At PVL-2+, literal datatypes SHOULD be represented by MeaningRefs in a datatypes pack (see Appendix A).

### 7.3 Scope
Scope fields:
- `as_of`: when the claim was asserted.
- `valid_time`: optional time interval the claim is about.
- `place`: optional structured location (profile-defined).
- `context_ref`: OPTIONAL; if present inside scope, it SHOULD match envelope `context_ref`.

### 7.4 Qualifiers
Qualifiers are additional predicate-object pairs that qualify the claim.

Rules:
- Qualifier predicates SHOULD come from a shared qualifiers pack to avoid ambiguity.
- Qualifiers are part of the canonical claim identity (they affect the artifact hash).

### 7.5 Graph artifact_type (bundling)
`artifact_type = "Graph"`

Graph bodies are OPTIONAL but RECOMMENDED for efficient publication of many claims.

Minimum Graph body:

```json
{
  "graph": {
    "nodes": [
      { "instance_id": "inst:uuid:..." , "labels": [ { "lang": "en", "text": "Paris" } ] }
    ],
    "claims": [
      { "claim_hash": "sha256:..." }
    ]
  }
}
```

Rules:
- A Graph MAY inline full claim objects, but then each inlined claim MUST either:
  - be separately hash-addressed and recoverable, OR
  - the Graph MUST define how claim IDs are derived from canonical inlined form.
- For interoperability, publishing standalone Claim artifacts is RECOMMENDED.

---

## 8. Evidence Model (normative)

### 8.1 EvidenceSubstrate artifact_type
`artifact_type = "EvidenceSubstrate"`

Body schema (minimum):

```json
{
  "substrate": {
    "payload_hash": "sha256:...",
    "media_type": "application/pdf",
    "size_bytes": 1234567,

    "locators": [
      { "kind": "url", "value": "https://example.org/doc.pdf" },
      { "kind": "ipfs", "value": "ipfs://bafy..." }
    ],

    "integrity": {
      "hash_alg": "sha256",
      "hash": "sha256:..."
    },

    "capture": {
      "captured_at": "2025-12-25T10:00:00Z",
      "captured_by": "did:key:...",
      "capture_method": "download | scan | recording | other"
    },

    "license": { "spdx": "CC-BY-4.0", "text": null },

    "inline_payload_base64": null
  }
}
```

Rules:
- `payload_hash` is REQUIRED at PVL-2+.
- `locators` are OPTIONAL and MUST be treated as non-authoritative hints.
- If `inline_payload_base64` is present, its decoded bytes MUST hash to `payload_hash`.

### 8.2 Selector objects
Selectors identify a relevant portion of a substrate or derived output.

Selector types (minimum):
- `ByteRangeSelector`:
  ```json
  { "selector_type": "byte_range", "start": 0, "end": 1024 }
  ```
- `TextSpanSelector` (see Appendix F for span units):
  ```json
  { "selector_type": "text_span", "buffer_id": "b0", "start": 10, "end": 42 }
  ```
- `TimeRangeSelector`:
  ```json
  { "selector_type": "time_range_ms", "start_ms": 120000, "end_ms": 132000 }
  ```
- `PageRegionSelector`:
  ```json
  { "selector_type": "page_region", "page": 3, "x0": 0.1, "y0": 0.2, "x1": 0.9, "y1": 0.3 }
  ```

Rules:
- Selectors MUST be deterministic and unambiguous.
- `start` is inclusive, `end` is exclusive for range selectors.
- Unknown selector types MUST cause PVL-2+ validation failure if the selector is required for interpretation.

### 8.3 Derivation artifact_type
`artifact_type = "Derivation"`

Derivation describes a transformation from one or more inputs to outputs.

Body schema (minimum):

```json
{
  "derivation": {
    "inputs": [
      { "artifact_hash": "sha256:..." }
    ],

    "method": {
      "kind": "tool",
      "tool_id": "ocr:tesseract",
      "tool_version": "5.3.0",
      "code_hash": "sha256:...",
      "params": { "lang": "eng" }
    },

    "determinism": {
      "declared": "deterministic | nondeterministic",
      "nondeterminism_sources": []
    },

    "outputs": [
      {
        "output_kind": "artifact",
        "artifact_hash": "sha256:..."
      }
    ],

    "produced_at": "2025-12-26T00:00:00Z"
  }
}
```

Rules:
- At PVL-2+, `inputs[]` MUST reference artifacts by hash.
- At PVL-3, if a derivation is part of a claim’s support chain, `determinism.declared` MUST be `deterministic` and `code_hash` MUST be present.

### 8.4 Evidence in claims: chains and support modes
Claim evidence entries (§7.1) MUST use `chain.substrate.artifact_hash` referencing an EvidenceSubstrate artifact.

Support modes (minimum set):
- `DIRECT_QUOTE`: claim supported by quoted text; selector SHOULD be a TextSpanSelector.
- `MEASUREMENT`: claim supported by a measured value; chain should include measurement metadata.
- `DERIVED`: claim supported by a derivation; derivations REQUIRED at PVL-2+.
- `ATTESTATION`: claim supported by a signed attestation artifact (extension-friendly).
- `OTHER`: explicitly unspecified; SHOULD be PVL-0/1 only.

### 8.5 Typed references (normative dependency roles)
Some references are normative dependencies; others are informational.

TypedReference object:

```json
{
  "ref_role": { "meaning_ref": { "pack": { "pack_id": "refs-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:ref:incorporates_by_reference" } },
  "target": { "artifact_hash": "sha256:..." }
}
```

Normative minimum ref roles (recommended core meanings):
- `INCORPORATES_BY_REFERENCE`: the target is part of the artifact’s authoritative meaning.
- `SUPPORTS`: target provides evidence/support but does not change meaning.
- `CONTEXTUALIZES`: target provides context (background), not support.
- `RETRACTS`: target retracts/contradicts a prior artifact.
- `SUPERSEDES`: target is replaced by this artifact.

Rules:
- PVL-2+ validators MUST treat `INCORPORATES_BY_REFERENCE` as a required dependency.
- Evidence references MUST NOT silently change the meaning of surface text. If incorporation is intended, it MUST be explicit via a normative ref role.

---

## 9. Templates and PVL-3 validation

### 9.1 Template meanings
Templates are PrismLex entries (`kind == "TEMPLATE"`) that define:
- required fields,
- allowed value kinds,
- constraints (cardinality, range, type),
- allowed predicates/roles.

### 9.2 TemplateInstance artifact_type
`artifact_type = "TemplateInstance"`

Minimum body:

```json
{
  "template_instance": {
    "template": { "meaning_ref": { "pack": { "pack_id": "templates-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:tpl:claim_located_in" } },
    "target": { "artifact_hash": "sha256:..." },
    "validation": {
      "expected": "pass",
      "notes": null
    }
  }
}
```

Rules:
- TemplateInstance does not change the target artifact’s meaning; it is a validation artifact.
- At PVL-3, claims that declare a template requirement MUST have a corresponding validation result (either embedded or linked) according to system policy. PRISM itself does not mandate a single validation-report transport, but does mandate determinism.

---

## 10. PRISM Verifiability Levels (PVL) (normative)

PVL classifies what must be true for an artifact to be considered valid at that level.

### 10.1 PVL-0 (Informal)
Allowed:
- unresolved meaning strings,
- locator-only evidence,
- missing dependencies.

Requirements:
- artifact must still be valid JSON and hashable/signable.
- validators MAY accept unknown artifact types as opaque blobs.

### 10.2 PVL-1 (Explicit but not fully grounded)
Requirements:
- All predicates used in Claim bodies MUST be either:
  - MeaningRefs, or
  - strings explicitly marked as unresolved (profile-defined).
- Evidence MUST be typed (e.g., `support_mode`) but may be locator-only.

### 10.3 PVL-2 (Grounded and checkable)
Requirements:
- All MeaningRefs used MUST resolve to locally available, hash-verified PackReleases.
- All PackReleases used MUST satisfy:
  - alias acyclicity,
  - definitional DAG and rootedness for DEFINED entries (§6.7–§6.8).
- Claim evidence MUST be content-addressed:
  - evidence substrates referenced by artifact hash,
  - substrates MUST include `payload_hash`,
  - selectors MUST be typed and deterministic when needed.
- Derivations used for support MUST be explicit artifacts referenced by hash.

Failure:
- If any required MeaningRef does not resolve, PVL-2 validation fails.
- If any required evidence substrate is missing, PVL-2 validation fails.
- If a selector type is unknown and required to interpret support, PVL-2 validation fails.

### 10.4 PVL-3 (Deterministic + schema-bound)
Requirements:
- PVL-2 requirements, plus:
- When a claim declares a template requirement (by policy or by explicit field), it MUST validate deterministically against that template.
- Derivations in support chains MUST be declared deterministic and MUST include a stable `code_hash`.
- Validators MUST produce consistent pass/fail results across implementations given the same dependency closure and enabled profiles.

---

## 11. Discovery overlays (non-normative)

Discovery overlays MUST NOT affect:
- artifact hash,
- PVL validity,
- meaning interpretation,
- template validation.

### 11.1 discovery_hints object (optional)
Example:

```json
{
  "discovery_hints": {
    "title": { "text": "Paris", "lang": "en" },
    "aliases": [ "City of Paris", "Paris, France" ],
    "keywords": [ "geography", "capital" ],
    "thumbnail_ref": { "artifact_hash": "sha256:..." }
  }
}
```

Rules:
- Removing discovery_hints MUST NOT change validity, meaning, or identity.
- Indexers MAY discard discovery_hints without loss of canonical content.

---

## 12. Compatibility, projections, and extension points

### 12.1 Extension artifact types
PRISM allows additional `artifact_type` values by ecosystem agreement.
Validators:
- MUST treat unknown artifact types as opaque at PVL-0/1,
- MUST fail PVL-2+ validation if an unknown type is required to interpret a claim/evidence chain.

### 12.2 PrismTags and PrismSigils
Compact tags and sigils are optional profiles:
- PrismTags provide a compact encoding for MeaningRefs (Appendix C).
- PrismSigils provide deterministic UI glyphs derived from meanings/tags (Appendix D).

### 12.3 PMF projection
Legacy/document-centric formats may be projected to/from PRISM using informative rules (Appendix E).

---

## 13. Security and privacy considerations

### 13.1 Do not treat media as self-authenticating
Hashes prove byte identity, not reality.
PRISM encourages explicit support modes, provenance, and contestability rather than implicit trust in media.

### 13.2 Executable payloads
Evidence substrates that are executables MUST be treated as unsafe to preview/run by default.
Systems SHOULD require scan/sandbox receipts by policy (outside PRISM scope).

### 13.3 PII and encryption
PRISM artifacts may be encrypted by outer envelopes; PRISM does not standardize encryption in core.
Publishers SHOULD avoid putting PII into public artifacts unless explicitly intended and governed.

### 13.4 Key management
PRISM signatures rely on external key infrastructure (e.g., DIDs).
Key revocation and trust anchors are out of scope for PRISM core.

---

## 14. Conformance checklist

A PRISM v1.0 implementation is conformant if it can:
1) Parse and canonicalize PRISM artifacts and compute artifact hashes (§4).  
2) Verify signatures for supported algorithms (§4.3).  
3) Resolve MeaningRefs against PackReleases and validate packs at PVL-2+ (§6).  
4) Parse and validate Claim and Evidence artifacts, including selectors and derivations (§7–§8).  
5) Enforce PVL requirements and report pass/fail at PVL-0..3 (§10).  
6) Treat discovery overlays as non-normative (§11).  

---

# Appendix A — Recommended core packs (non-normative)

To enable interoperability, ecosystems SHOULD publish and reuse shared packs for:
- `roles-core`: subject/object, qualifier roles, graph roles
- `predicates-core`: common predicates (is_a, located_in, has_part, authored_by, etc.)
- `operators-core`: AND/OR/NOT/EXISTS/FORALL/EQUALS
- `relations-core`: deprecated/replaced_by/alias_of/is_a/broader/narrower
- `datatypes-core`: string/number/boolean/date/time/duration/url/hash
- `refs-core`: typed reference roles (incorporates/supports/contextualizes/retracts/supersedes)
- `evidence-core`: evidence kinds and support_mode meanings
- `risk-core`: risk categories for safety gating (for downstream engines)

---

# Appendix B — Design invariants (non-normative)

1) **Canonical vs derived:** PRISM artifacts are the canonical interchange units (content-addressed and immutable). Indexes/summaries/rankings are derived. Downstream stacks MAY persist derived outputs as PRISM artifacts for audit/convenience, but they remain derived and MUST be labeled as such (non-claims / non-authoritative).  
2) **No silent meaning change:** evidence and discovery metadata must not change meaning unless explicitly incorporated.  
3) **Grounding discipline:** PVL-2+ meaning definitions should be acyclic and rooted.  
4) **Hash identity:** byte-level changes imply identity changes.  
5) **Pluralism:** multiple packs, lenses, and governance contexts can coexist.  

---

# Appendix C — PrismTag Encoding Profile (PTEP) (normative optional)

**Goal:** Provide a compact 64-bit encoding (“tag64”) that resolves to MeaningRefs while keeping canonical identity anchored by `(pack_release_hash, entry_uid)`.

> Key rule: PrismTags are **encodings**, not identities.

## C.1 Terminology
- **tag64:** unsigned 64-bit integer serialized big-endian.
- **namespace_table:** document-local mapping from `ns_code` to PackRef.
- **sense_id:** 24-bit compact entry identifier within a namespace table entry.

## C.2 Binary layout (tag64)
PrismTag is a 64-bit unsigned integer serialized in **big-endian** for interchange.

Bit numbering: bit 0 is least significant.

```
63..56  CRC8
55..54  CTRL (2)
53..50  NS_CODE (4)
49..38  ASSERT_FALSE (12)
37..26  ASSERT_TRUE  (12)
25..24  TYPE (2)
23..0   SENSE_ID (24)
```

### C.2.1 CTRL field
- `00` = baseline tag64 (this profile)
- `01`..`11` reserved for future extension

If `CTRL != 00`, the tag MUST be treated as invalid unless an extension profile is enabled.

### C.2.2 CRC8
CRC is computed over bits `0..55` inclusive (low 56 bits).

CRC-8 parameters:
- polynomial `0x07`
- init `0x00`
- refin=false, refout=false
- xorout `0x00`

If CRC validation fails, the tag64 MUST be treated as invalid.

## C.3 TYPE and ASSERT masks

### C.3.1 TYPE (2 bits)
Interpretation (recommended):
- `00` = Virtual
- `01` = Physical
- `10` = Conceptual
- `11` = Hybrid/Other

TYPE is an encoding hint; it does not replace MeaningRefs.

### C.3.2 ASSERT_TRUE / ASSERT_FALSE (12 + 12 bits)
These masks encode open-world property assertions.

Property index order (bit 0..11):
0 `_active`  
1 `_interact`  
2 `_mutable`  
3 `_relational`  
4 `_complex`  
5 `_infinite`  
6 `_temporal`  
7 `_mobile`  
8 `_natural`  
9 `_incidental`  
10 `_autonomous`  
11 `_needenergy`

Semantics:
- If bit i set in ASSERT_TRUE: writer asserts property i is true.
- If bit i set in ASSERT_FALSE: writer asserts property i is false.
- If neither set: unknown (open world).
- If both set: contradiction; validators SHOULD surface as an error or warning.

## C.4 Namespace Table (document-local)
A PrismTag cannot resolve without a namespace table.

Namespace table schema:

```json
{
  "namespace_table": [
    { "ns_code": 0, "pack": { "pack_id": "predicates-core", "release": { "hash": "sha256:..." } } },
    { "ns_code": 1, "pack": { "pack_id": "roles-core", "release": { "hash": "sha256:..." } } }
  ]
}
```

Rules:
- ns_code MUST be 0..15.
- A tag64’s NS_CODE selects a PackRef from the namespace table.
- Implementations MUST reject namespace table collisions (duplicate ns_code).

## C.5 Resolving tag64 → MeaningRef
Resolution algorithm:
1) Validate CTRL and CRC8.
2) Extract `ns_code` and `sense_id`.
3) Lookup `PackRef` in namespace_table by ns_code.
4) Resolve `sense_id` to `entry_uid` using the pack’s compact ID mapping.

Rules:
- At PVL-2+, resolution MUST end in a MeaningRef that resolves to a verified pack entry.
- If compact ID mapping is unavailable, the tag cannot resolve and MUST be treated as invalid at PVL-2+.

## C.6 Canonical textual encoding
Canonical textual form:
- `prismtag:0x` + 16 hex digits (lowercase), representing the big-endian 64-bit value.

Example:
- `prismtag:0x0123456789abcdef`

Base64url form MAY be supported as a non-canonical alternate.

## C.7 Role Table for PrismRoles compression (optional)
Projection formats may include a `role_table` to compress role MeaningRefs similarly to namespace_table. This is informative unless a projection profile declares it normative.

## C.8 When to use PrismTags vs MeaningRefs
Recommended:
- Use MeaningRefs for canonical storage and interchange.
- Use PrismTags for compact repeated references inside a document projection.

## C.9 Segmenting to avoid namespace limits
Because NS_CODE is 4 bits, only 16 namespaces can be referenced in one namespace_table.
If more are needed:
- segment the projection, OR
- hybridize: use MeaningRefs for less frequent entries, tags for frequent ones.

---

# Appendix D — PrismSigil Rendering Profile (PSRP) (normative optional)

PrismSigils are deterministic, optional UI glyphs derived from either:
- MeaningRefs (preferred), or
- PrismTags (compatibility mode).

Sigils MUST NOT be treated as canonical meaning.

## D.1 Canonical sigil representation
A sigil is defined by:
- `radical` (TYPE class),
- `modifiers` (asserted flags),
- `fingerprint` (uniqueness strokes).

Implementations MUST produce identical canonical sigil components given the same input identity under the selected mode.

## D.2 Input modes
- **Meaning-derived mode (RECOMMENDED):** fingerprint derived from `(pack_release_hash, entry_uid)`.
- **Tag-derived mode (COMPAT):** fingerprint derived from `tag64_bytes_be`.

Mode MUST be declared by the renderer.

## D.3 Deterministic derivation rules

### D.3.1 Radical (from TYPE)
Map TYPE to radical:
- Virtual → circle-like radical
- Physical → square-like radical
- Conceptual → triangle-like radical
- Hybrid/Other → diamond-like radical

(Exact stroke geometry defined in §D.4.)

### D.3.2 Modifiers (from ASSERT masks)
Each asserted property bit adds a modifier stroke at a canonical location.

If a property is asserted both true and false, renderers SHOULD add a conflict marker.

### D.3.3 Fingerprint
Fingerprint is a small number of micro-strokes derived from a hash.

Meaning-derived fingerprint:
- bytes = UTF-8 of `pack_release_hash + ":" + entry_uid`
- h = SHA-256(bytes)
- take first 20 bits as fingerprint bits

Tag-derived fingerprint:
- bytes = tag64 in big-endian 8 bytes
- h = SHA-256(bytes)
- take first 20 bits as fingerprint bits

Fingerprint bits are rendered as a sequence of micro-strokes (see §D.4.4).

## D.4 Rendering standard (PSRS-compatible)
This section defines a canonical coordinate system and stroke rules so different renderers produce visually consistent sigils.

### D.4.1 Coordinate system
Normalized square canvas:
- x and y in [0.0, 1.0]
- origin bottom-left
- center C = (0.5, 0.5)

### D.4.2 Stroke style
Renderers MUST use:
- round line caps
- round line joins
- consistent stroke width relative to glyph size

### D.4.3 Radical geometry
- Virtual: circle centered at C, radius 0.35
- Physical: square with corners at (0.15,0.15) and (0.85,0.85)
- Conceptual: triangle with vertices (0.5,0.85), (0.15,0.15), (0.85,0.15)
- Hybrid/Other: diamond with vertices (0.5,0.9), (0.1,0.5), (0.5,0.1), (0.9,0.5)

### D.4.4 Modifier positions (12 slots)
Define 12 evenly spaced anchor points around the radical boundary.
Property bit i uses anchor i.

True-assertion: short outward tick.
False-assertion: short inward tick.

### D.4.5 Fingerprint micro-strokes
Fingerprint bits 0..19 map to a 5x4 grid in the interior.
A bit set draws a dot (or short stroke) at the corresponding cell.

## D.5 Safety and policy interactions
Sigils are UI; they MUST NOT bypass safety policies.
Implementations SHOULD allow blurring or suppressing sigils for sensitive content classes.

---

# Appendix E — PMF Projection Notes (PrismTags + PrismRoles) (informative)

This appendix describes how a document-centric “Prism Message Format” can be projected to/from PRISM artifacts:
- surface text buffers,
- token spans,
- PrismTags for compact meaning references,
- PrismRoles graphs for compositional structure.

This projection is informative; ecosystems may standardize a separate PMF profile.

Key invariant:
- projections MUST NOT change canonical meaning; PRISM artifacts remain authoritative.

---

# Appendix F — Encoding, Normalization, and Span Rules (normative optional profile)

**Status:** Normative optional profile.  
**Goal:** Make span selectors and identity-string handling consistent across implementations.

## F.1 UTF-8 everywhere
All PRISM artifact bytes MUST be UTF-8 encoded (no BOM).

## F.2 Unicode normalization
- Strings that participate in identity material (e.g., `entry_uid`, `pack_id` if used in UI IDs, etc.) SHOULD be NFC-normalized.
- Surface text and quoted excerpts MUST preserve original code points; do not normalize display text silently.

## F.3 Character indexing and spans
For `TextSpanSelector`, span units are **Unicode scalar values** (code points), not bytes.

- `start` inclusive, `end` exclusive.
- Spans are measured over the chosen `buffer_id` string.

Grapheme clusters are informative; implementations MAY offer grapheme-aware UX, but canonical spans use scalar values.

## F.4 Canonicalization for hashing/signing
Hash and signing MUST use canonical JSON (§4), not ad-hoc whitespace normalization.

---

# Appendix G — Prism Social Signals (PSS) Profile (normative optional)

**Status:** Normative optional profile.  
**Goal:** Represent social/affective signals as structured artifacts that can be attached to content or interactions.

PSS is OPTIONAL and MUST NOT affect the semantic truth-conditions of claims.

## G.1 Definitions
- **Social Signal:** a structured observation about affect, stance, intent, or nonverbal communication.
- **Signal Channel:** text, voice, posture, gesture, appearance, other.
- **Affective stance:** dimensions like valence, arousal, dominance (profile-defined).

## G.2 Artifact type
`artifact_type = "SocialSignal"`

Minimum body:

```json
{
  "pss": {
    "about": { "artifact_hash": "sha256:..." },
    "channel": "text | voice | posture | gesture | appearance | other",
    "stance": {
      "valence": 0.2,
      "arousal": 0.7,
      "dominance": 0.4
    },
    "labels": [
      { "meaning_ref": { "pack": { "pack_id": "pss-core", "release": { "hash": "sha256:..." } }, "entry_uid": "uid:pss:urgent_request" } }
    ],
    "evidence": [
      {
        "support_mode": "DERIVED",
        "chain": {
          "substrate": { "artifact_hash": "sha256:..." },
          "selector": { "selector_type": "time_range_ms", "start_ms": 1200, "end_ms": 3400 },
          "derivations": []
        }
      }
    ],
    "notes": { "text": "Observed in call recording.", "lang": "en" }
  }
}
```

Rules:
- Social signals MAY carry evidence chains like claims do.
- Social signals SHOULD be scoped to a context (via envelope `context_ref`) when used in communal governance.

## G.3 PVL interaction
- PVL-0/1: social signals may be informal.
- PVL-2+: label meanings MUST resolve to verified packs; evidence substrates must be content-addressed.
- PVL-3: deterministic extraction pipelines may be required by policy if signals are machine-derived.

## G.4 Templates for PVL-3 (PSS)
Ecosystems MAY define templates such as:
- `PSS/SOCIAL_SIGNAL_BASIC`
- `PSS/SOCIAL_SIGNAL_WITH_CONTENT`

Template enforcement follows §9.

## G.5 Security and governance
PSS is vulnerable to manipulation. Systems SHOULD:
- require signatures,
- provide contestation mechanisms via governance receipts,
- limit ranking impact via policy.

---

# Appendix H — Commitment Log / Transparency Profile (normative optional)

**Goal:** Provide an append-only publication commitment mechanism to reduce equivocation (history rewriting) without requiring global consensus.

## H.1 CommitmentLogHead artifact_type
`artifact_type = "CommitmentLogHead"`

Body schema (minimum):

```json
{
  "log_head": {
    "log_id": "did:key:z6M...#prism-log",
    "tree_size": 123456,
    "root_hash": "sha256:...",
    "prev_head": { "artifact_hash": "sha256:..." },
    "issued_at": "2025-12-26T00:00:00Z"
  }
}
```

Rules:
- `root_hash` MUST be the Merkle root of an append-only log of artifact hashes.
- `tree_size` MUST be monotonically increasing for a given log_id.
- Heads SHOULD be signed (envelope signatures).

## H.2 Inclusion and consistency proofs (transport-agnostic)
PRISM does not mandate a proof transport API, but defines proof objects that may be returned by stores.

Inclusion proof object:

```json
{
  "proof_type": "merkle_inclusion_v1",
  "log_id": "did:key:...#prism-log",
  "artifact_hash": "sha256:...",
  "leaf_index": 4242,
  "audit_path": ["sha256:...", "sha256:..."],
  "head": { "artifact_hash": "sha256:..." }
}
```

Consistency proof object:

```json
{
  "proof_type": "merkle_consistency_v1",
  "log_id": "did:key:...#prism-log",
  "from_tree_size": 1000,
  "to_tree_size": 2000,
  "proof": ["sha256:...", "sha256:..."],
  "from_head": { "artifact_hash": "sha256:..." },
  "to_head": { "artifact_hash": "sha256:..." }
}
```

## H.3 PVL interaction
Commitment logs are OPTIONAL for PVL validity, but ecosystems MAY adopt policies like:
- “Claims of certain predicates must be committed to a witnessed log to be admissible.”

PRISM remains neutral; it provides the mechanism.

---
