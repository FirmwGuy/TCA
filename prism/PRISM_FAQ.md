# PRISM FAQ
**Applies to:** PRISM v1.0 
**Last updated:** 2025-12-26

This FAQ answers common questions for implementers and publishers working with PRISM v1.0: packs (meanings), claims, evidence chains, PVL (verifiability levels), optional profiles (PrismTags, PrismSigils, PSS), and how PRISM relates to stores/indexers like NEXUS and governance layers like Weave.

---

## Getting started

### 1) What is PRISM, in one sentence?
PRISM is a protocol for publishing **content-addressed, optionally signed artifacts** that encode **meanings**, **claims**, and **evidence chains**, with a **verifiability grade (PVL)** describing what can be mechanically checked.

### 2) What does PRISM *not* do?
PRISM does **not**:
- decide truth,
- decide who is authoritative,
- define governance procedures,
- define search/ranking,
- define network transport.

PRISM provides **canonical artifacts** and **validation rules**; other layers decide legitimacy, discovery, and policy.

### 3) What’s the difference between “claim” and “fact”?
In PRISM core:
- **Claim** = an atomic assertion artifact (canonical and hash-addressed).
- **Fact** = a *view* (e.g., “a claim treated as fact in a specific governance context”).

This avoids accidental conflation of “verifiable structure” with “true in the world.”

### 4) What is PVL and why is it central?
**PVL (PRISM Verifiability Levels)** classifies how much a validator can mechanically verify:
- PVL-0: informal, minimal checks
- PVL-1: explicit structure, partial grounding
- PVL-2: grounded meanings + content-addressed evidence, checkable closure
- PVL-3: deterministic pipelines + schema-bound template validation

PVL is about **verifiability**, not truth.

### 5) Do I have to use all of PRISM?
No. Many ecosystems start with:
- PackRelease (meanings)
- Claim
- EvidenceSubstrate (content-addressed evidence)
Then add:
- Derivation (tool pipelines)
- Templates (PVL-3)
- Commitment logs (anti-equivocation)
- Optional profiles (PrismTags/Sigils/PSS)

---

## Meanings and packs (PrismLex)

### 6) Why have packs at all?
Packs make meanings:
- explicit,
- versioned,
- hash-pinned,
- shareable across communities,
- and machine-checkable at PVL-2+.

Without packs, you end up with “stringly typed” semantics and implicit assumptions.

### 7) What is a MeaningRef?
A **MeaningRef** is the canonical pointer to a meaning:
- `(pack_release_hash, entry_uid)` plus a human-friendly `pack_id`.

At PVL-2+, MeaningRefs must resolve to a locally available, hash-verified PackRelease.

### 8) What is the difference between PRIMITIVE and DEFINED meanings?
- **PRIMITIVE**: a root concept (no definitional dependencies).
- **DEFINED**: a meaning with a **canonical definition graph (PDG)** that is machine-checkable and grounded.

### 9) What is the PDG discipline and why does it matter?
PDG (Prism Definition Graph) makes definitions:
- explicit graphs with typed nodes/edges,
- dependency-extractable,
- checkable for **acyclicity** and **rootedness**.

This prevents hidden circular definitions and makes meaning “supply chains” auditable.

### 10) What does “rootedness” mean?
Every DEFINED meaning’s dependency chains must ultimately terminate in PRIMITIVE meanings (or meanings with no definitional dependencies). If definitions rely on other definitions forever, validators can’t ground anything.

### 11) Can meanings depend on empirical claims or evidence?
They can be **supported by** evidence/claims, but meanings should not be **held hostage** to them. If the evidence disappears, the meaning should remain resolvable and identifiable.

A good pattern:
- meaning identity and PDG grounding are semantic,
- evidence trails are optional support artifacts and may influence PVL or policy, but not identity.

### 12) Are compact IDs required?
No. Compact IDs are optional encodings for efficiency. Canonical identity is MeaningRef. Compact IDs must never be the *only* interoperability path at PVL-2+.

---

## Claims (“facts”)

### 13) What does a Claim look like in PRISM?
A Claim minimally includes:
- subject (instance_id / meaning / literal)
- predicate (MeaningRef)
- object (instance_id / meaning / literal)
- optional scope, qualifiers
- evidence chains (recommended at PVL-2+)

### 14) Can Claims be bundled for efficiency?
Yes. Use Graph artifacts to bundle many claims/nodes. For maximum interoperability:
- also publish standalone Claim artifacts (recommended), or
- define a deterministic claim-id derivation rule if claims are inlined.

### 15) How do qualifiers work?
Qualifiers are additional predicate-object pairs that refine a claim (confidence, method, unit, etc.). They are part of the canonical claim content and therefore affect the artifact hash.

---

## Evidence and AI-era durability

### 16) Why does PRISM treat URLs as non-authoritative?
Because links rot, platforms vanish, access is unequal, and platform content can be altered. A URL can be a **locator hint**; it is not durable evidence.

### 17) What counts as evidence in PRISM?
Evidence is represented as an **evidence chain**:
1) **EvidenceSubstrate** (content-addressed bytes, or hash to bytes)
2) optional **Selector** (what portion is relevant)
3) optional **Derivation(s)** (explicit transformations like OCR, parsing, transcription)
4) a **support assertion** (how this chain supports the claim)

### 18) How does this help against AI-generated fake media?
PRISM doesn’t assume media is self-authenticating. Instead:
- it forces explicit provenance and transformation chains,
- allows attestations and contestation as first-class,
- and separates “bytes are these” (hash) from “reality is that” (a claim that can be challenged).

### 19) What is a Selector and why is it important?
Selectors pin “what part matters” deterministically:
- page+region in a PDF scan
- time range in audio/video
- text spans in extracted text
This increases precision and makes evidence review reproducible.

### 20) What is a Derivation?
A Derivation records a tool pipeline step:
- inputs (by artifact hash)
- method identity (tool id/version, code hash)
- determinism declaration
- outputs (by artifact hash)

At PVL-3, derivations that support claims must be deterministic and include code hashes.

### 21) Do I have to inline payload bytes in EvidenceSubstrate?
No. EvidenceSubstrate can:
- include only hashes + locators, or
- optionally inline bytes (e.g., base64)
Stores may enforce size/visibility policies.

### 22) What happens if evidence bytes are unavailable later?
PRISM distinguishes:
- “we can verify the hash identity” vs “we can fetch the bytes.”
An ecosystem can surface degradation explicitly:
- claim remains a canonical artifact,
- evidence chain exists,
- availability can drop, and PVL validation may fail at higher levels if required dependencies are missing.

---

## PVL (Verifiability Levels)

### 23) Who sets PVL?
The publisher declares `pvl` in the envelope. Validators can compute whether the artifact actually **passes** that PVL, given available dependencies and enabled profiles.

### 24) Can an artifact claim PVL-3 but fail PVL-3 validation?
Yes. That’s expected. Implementations should treat it as:
- declared PVL vs validated PVL,
- with explicit failure reasons.

### 25) Does PVL imply truth?
No. PVL is a statement about **mechanical verifiability**, not correctness in the external world.

### 26) How should ecosystems use PVL?
Common uses:
- filter: “show only PVL≥2”
- policy: “medical predicates require PVL-3”
- ranking signal: “prefer PVL-2+ with deterministic derivations”
- safety: “PVL-0 unknown evidence gets metadata-only preview”

---

## Hashing, signatures, and immutability

### 27) Why content-addressed artifacts?
Content addressing gives:
- tamper evidence,
- stable identity under rehosting,
- easy deduplication,
- reliable dependency graphs.

### 28) Do I need signatures?
Signatures are optional in PRISM core, but strongly recommended at PVL-2+ and often required by policy.

Hashes answer: “are these bytes the same?”
Signatures answer: “who asserted/published these bytes?”

### 29) Is PRISM a blockchain?
No. PRISM does not require global ordering or consensus.
It can optionally support **commitment logs** (append-only transparency) to reduce equivocation, but this is not a global chain.

---

## Commitment logs (optional transparency)

### 30) What problem do commitment logs solve?
They help prevent **history rewriting / equivocation**:
- publishing claim A,
- later denying it existed by selectively showing only newer claims.

A commitment log provides append-only commitments with inclusion/consistency proofs.

### 31) Are commitment logs required?
No. They’re an optional profile. Ecosystems may require them for:
- high-impact claims,
- featured/certified lanes,
- accountability regimes.

### 32) Do commitment logs make claims true?
No. They make publication histories harder to rewrite.

---

## Discovery hints and derived products

### 33) What are discovery hints?
Discovery hints are optional metadata to help:
- indexing,
- search,
- UI presentation.

They must remain non-normative: removing them must not change meaning, PVL, or identity.

### 34) Are embeddings or summaries part of PRISM?
They can be represented as derived artifacts or overlays, but PRISM’s core line is:
- canonical artifacts are authoritative,
- derived products must be labeled as derived and discardable.

---

## Optional profiles and appendices

### 35) What are PrismTags?
PrismTags are an optional **compact 64-bit encoding** that can resolve to MeaningRefs via a namespace table and compact-id mapping. They are:
- encodings (compression),
- not meaning identity.

### 36) When should I use PrismTags?
Use PrismTags when:
- bandwidth/size matters,
- you have repetitive meaning references (e.g., document projections),
- you can also provide namespace tables.

For canonical artifacts and interoperability, prefer MeaningRefs.

### 37) What are PrismSigils?
Sigils are optional deterministic UI glyphs derived from:
- MeaningRefs (preferred), or
- PrismTags (compat mode).
They are for human scanning and must never be treated as semantics.

### 38) What is PSS?
PSS (Prism Social Signals) is an optional profile for representing social/affective signals as structured artifacts. It must not be treated as semantic truth conditions for claims.

### 39) Why keep PSS optional?
Because:
- it’s domain-specific,
- sensitive,
- and governance-heavy.
PRISM supports it, but ecosystems should adopt it only with strong policy controls.

---

## Privacy, safety, and executables

### 40) How should PRISM handle PII?
PRISM doesn’t impose a single privacy regime, but encourages:
- visibility controls in envelopes,
- encryption outside core when needed,
- explicit governance policies in higher layers.

Avoid leaking PII into public artifacts unless intended.

### 41) What about executable artifacts?
Treat them as unsafe by default:
- no auto-run,
- no auto-preview,
- require scans/sandbox receipts by policy.

PRISM can represent scan reports as artifacts, but does not mandate a security policy.

---

## PRISM vs NEXUS vs governance layers

### 42) How does PRISM relate to NEXUS?
PRISM defines the canonical artifacts and validation rules.
NEXUS stores those artifacts, materializes graph views, builds indexes, and executes discovery pipelines and policies.

### 43) How does PRISM relate to governance layers like Weave?
PRISM is governance-neutral. Governance layers can:
- define receipts/outcomes,
- decide which policies are active,
- determine legitimacy of claims/packs in a context.

PRISM artifacts can reference context identifiers, but PRISM itself does not decide authority.

---

## Implementation tips

### 44) What should I implement first?
A minimal useful stack:
1) canonical hashing + envelope parsing  
2) PackRelease validation (MeaningRefs resolve)  
3) Claim parsing + evidence substrate hashing  
4) PVL-2 validator (grounded closure checks)

### 45) What are common failure modes?
- unresolved MeaningRefs at PVL-2+
- circular definitions in packs
- selectors that aren’t deterministic or aren’t understood
- evidence chains missing substrates
- non-canonical JSON hashing mismatches

### 46) How do I test interoperability?
- Create a corpus of artifacts with known hashes.
- Verify multiple implementations compute identical hashes.
- Validate PVL levels and compare failure reasons.
- Include packs with intentional cycles to ensure validators reject them.

### 47) Can ecosystems extend PRISM?
Yes:
- add artifact types,
- add selector types,
- add signature algorithms,
- add policy hooks in higher layers.

But for PVL-2+, unknown required types should fail closed, because ambiguity undermines verifiability.

---

## Quick glossary

- **Artifact**: canonical PRISM unit; envelope + body; hash-addressed  
- **PackRelease**: meaning pack artifact; defines MeaningEntries  
- **MeaningRef**: canonical meaning pointer = (pack release hash, entry_uid)  
- **Claim**: atomic assertion artifact  
- **EvidenceSubstrate**: content-addressed evidence bytes (or hash to bytes)  
- **Selector**: deterministic pointer to a portion of a substrate  
- **Derivation**: explicit transformation pipeline step  
- **PVL**: PRISM Verifiability Level (0–3)  
- **Discovery hints**: non-normative metadata for search/UI  
- **Commitment log**: optional append-only transparency mechanism  
- **PrismTags**: optional compact meaning encoding  
- **PrismSigils**: optional deterministic glyph rendering profile  
- **PSS**: optional social signals profile

---
