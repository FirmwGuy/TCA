# NEXUS v1.0 — Claim Graph Knowledgebase Runtime (Discovery + Projections + Ingestion)
SPDX-FileCopyrightText: © 2025–present Victor M. Barrientos <firmw.guy@gmail.com>
SPDX-License-Identifier: CC-BY-SA-4.0
**NEXUS = Neutral Engine for eXecutable, User‑Scoped Search**
**Status:** Draft for implementation 
**Last updated:** 2025-12-26  
**Companion specs:** PRISM v1.0, WEAVE (Constitution + WCIC), WKGC v0.1, WCL v0.1, **NEXUS-POLICIES v0.1**, **PSR v0.1**, **PSA v0.1**

---

## 0. Purpose

NEXUS is an execution runtime for an online knowledgebase whose canonical substrate is a graph of **PRISM artifacts** governed by **WEAVE**.

NEXUS provides four top-level capabilities:

1) **Canonical storage** of PRISM artifacts by content hash (envelopes preserved).
2) **Verifiability computation**: PVL validation (declared vs validated) over dependency closure.
3) **Governance execution**: lane/status computation from WEAVE receipts/outcomes and policy adoption.
4) **Derived experiences**:
   - **Discovery** (search pipeline): safety gating → PVL gating → ranking → explanations.
   - **Projections** (pages): dictionary/encyclopedia/event/digest pages built from bounded graph selection, rendered deterministically via PSR.
   - **Ingestion** (text → candidates): PSA-driven extraction of candidate claims/entities/meaning mappings from user text, routed into review lanes.

NEXUS does **not** define:
- semantic meaning identity (PRISM packs do),
- legitimacy/authority (WEAVE does),
- truth,
- network transport (peer replication), beyond import/export contracts.

> **Key principle:** NEXUS executes declared policies and computes derived views from canonical artifacts and receipts; it does not invent meanings, facts, or legitimacy.

---

## 1. Conformance language

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in RFC 2119.

---

## 2. Design principles

### 2.1 Canonical vs derived (hard boundary)
**Canonical inputs** to NEXUS are:
- PRISM artifacts (claims, packs, evidence substrates, selectors, derivations, receipts, outcomes, policy artifacts),
- Editions (signed manifests) where used for federation.

**Derived products** are:
- indexes (lexical/graph/vector/hybrid),
- embeddings,
- rankings,
- previews/thumbnails,
- page projections (HTML/Markdown),
- candidate extraction outputs (CandidateBundles),
- caches and feature vectors.

Derived products MUST be:
- discardable without loss of canonical meaning,
- provenance-tagged to canonical inputs,
- reconstructible within declared replay windows (where required by policy).

Derived products MAY be persisted as PRISM artifacts for convenience/audit (e.g., CandidateBundles or snapshots), but they remain **derived** and MUST be clearly labeled as such (non-claims / non-authoritative).

### 2.2 Safety before relevance
Discovery and projection MUST enforce:
1) safety gating (hard constraints)
2) PVL/validity gating (hard constraints, policy-controlled)
3) ranking / ordering (soft)

### 2.3 Pluralism without hidden authority
NEXUS supports multiple:
- contexts and lenses,
- index providers,
- projection renderers (PSR always available; AI optional and constrained),
- ingestion analyzers (PSA deterministic or model-assisted).

No single index, renderer, or analyzer is allowed to become “the truth” by default.

### 2.4 Runtime neutrality
NEXUS MUST surface:
- which contexts and policies are active,
- why an item is shown, hidden, gated, or ranked,
- and why a page contains what it contains.

### 2.5 Evidence-first
Claims and ingestion outputs MUST be anchored to durable evidence objects:
- URLs are locators, not evidence identity.
- Evidence identity is content-addressed.

---

## 3. Terminology

- **Artifact hash:** content hash of a PRISM artifact (e.g., `sha256:...`).
- **MeaningRef:** pointer to a PackRelease hash + entry UID.
- **Claim:** PRISM `Claim` artifact (atomic assertion).
- **Evidence chain:** substrate → selector(s) → derivation(s) → support mode.
- **PVL:** PRISM Verifiability Level (0–3).
- **Context:** WEAVE governance scope (`context_id`).
- **Receipt / Outcome:** governance artifacts that establish lane/status and decisions.
- **Lane:** lifecycle tier (draft/proposed/admitted/featured/suppressed/quarantine).
- **Lens:** view configuration: contexts + policy set + editions + index/projection settings + user safety (cannot bypass context floor).
- **Projection:** derived page view (meaning/entity/event/digest).
- **PSR:** PRISM Surface Realizer (deterministic renderer).
- **PSA:** PRISM Semantic Analyzer (candidate extractor).
- **CandidateBundle:** PSA output bundle containing candidate claims/entities/mappings, not canonical claims by itself.
- **Persisted-derived artifact:** a derived product stored as a PRISM artifact for audit/convenience (e.g., CandidateBundle or snapshots), still derived/non-authoritative.
- **Edition:** signed manifest listing artifact hashes (unit of federation/audit).
- **IndexSnapshot:** auditable record of an index build.
- **ProjectionSnapshot:** auditable record of a projection build.
- **Replay record:** auditable record of a search run or projection run.

---

## 4. Responsibilities and prohibited behaviors

### 4.1 NEXUS MUST
1) Store PRISM artifacts by hash and preserve canonical bytes.
2) Verify artifact hashes and verify signatures when present (per PRISM rules).
3) Resolve MeaningRefs against hash-verified packs at PVL-2+.
4) Compute and expose **declared PVL vs validated PVL** with explainable failures.
5) Materialize a queryable claim graph (derived) from Claim/Graph artifacts.
6) Ingest and interpret governance artifacts needed for context execution:
   - ContextDeclarations,
   - receipts/outcomes,
   - policy adoption receipts (or equivalent).
7) Compute lane/status strictly from receipts/outcomes and editions (no mutable flags).
8) Implement discovery pipeline stages with provenance and explanations.
9) Prevent leakage of sensitive content into unauthorized indexes and projections.
10) Implement projection pipeline(s) and provide deterministic rendering via PSR.
11) Implement ingestion/extraction hooks for PSA outputs, routed to review lanes by default.
12) Enforce authorship constraints required by TCA/WEAVE contexts (e.g., Human Commons canonical writes require an accountable human principal authorization/signature as defined by the context’s identity policy).

### 4.2 NEXUS MUST NOT
- decide authority or legitimacy,
- infer “truth,”
- silently change lane membership without receipts/outcomes,
- treat popularity or engagement as authority unless explicitly permitted by context policy (discouraged),
- auto-preview risky payloads by default (executables, graphic media),
- present AI projections as canonical content,
- auto-admit PSA outputs into shared lanes by default.

---

## 5. Reference architecture (modules)

A conforming NEXUS implementation SHOULD include:

**Canonical**
1) Artifact Store  
2) Pack Store  
3) Governance Store  

**Validation**
4) PVL Validator (closure-aware)  

**Derived**
5) Claim Graph Store  
6) Index Subsystem (multiple providers)  
7) Policy Executor (deterministic)  
8) Discovery Runtime (pipeline)  

**Pages**
9) Projection Builder (bounded selection + importance tiers)  
10) Renderer(s):
    - PSR deterministic renderer (REQUIRED)
    - optional AI renderer (OPTIONAL; policy-controlled)

**Ingestion**
11) Ingestion Runtime:
    - Evidence ingestion (source anchoring)
    - PSA runner (REQUIRED hook; PSA engine itself may be pluggable)
    - CandidateBundle store (derived; may be persisted as non-claim PRISM artifacts for audit)
    - Review queue integration (proposed/quarantine lanes)

**Audit**
12) Replay/Audit Recorder (IndexSnapshot + ProjectionSnapshot + replay records)

---

## 6. Canonical storage and verification

### 6.1 Artifact store
Minimum operations:
- `put(bytes) -> artifact_hash`
- `get(hash) -> bytes`
- `has(hash) -> bool`
- `verify(hash) -> { ok, reason, signatures_verified[] }`

Rules:
- The hash MUST match PRISM canonical hashing rules.
- Unknown signature algorithms MUST be reported, not silently accepted.

### 6.2 Pack store and meaning resolution
At PVL-2+ interpretation time:
- MeaningRefs MUST resolve to locally available, hash-verified PackReleases.

NEXUS SHOULD cache:
- `(pack_hash, entry_uid) -> meaning entry`
- derived predicate classifications used by policies (predicate kinds, risk kinds, etc.).

### 6.3 Governance store
NEXUS stores and exposes:
- ContextDeclarations (versioned),
- receipts/outcomes,
- policy adoption receipts/outcomes,
- optional role registries/key rotation receipts if a context uses them.

Status computation MUST be reproducible from stored governance artifacts.

---

## 7. PVL validation (declared vs validated)

### 7.1 Validation modes
NEXUS MUST support:
- `declared-only`
- `validated` (target PVL specified)

Outputs:
- pass/fail/unknown,
- failure reasons,
- missing dependency list.

### 7.2 Cache keying
PVL validation results MUST be keyed by:
- artifact_hash,
- target PVL,
- enabled profiles set hash,
- validator code hash/version.

---

## 8. Claim graph materialization (derived)

NEXUS MUST materialize at least:
- instances/nodes,
- claims/edges (subject, predicate MeaningRef, object),
- time/scope,
- qualifiers,
- evidence summaries (chain count, selector precision, determinism flags),
- governance status per context (lane, attested/contested, supporting receipt hashes),
- PVL summaries (declared and validated).

The graph MUST be rebuildable from canonical artifacts + active packs + governance inputs.

---

## 9. Lenses and policy activation

### 9.1 Lens definition
A Lens minimally includes:
- contexts in scope (context_id + declaration hash),
- adopted policy set hashes (Safety/Ranking/Quality/Admission/Service/Index/**Projection**/**Ingestion**),
- active editions/snapshots,
- index provider(s) allowed,
- lane visibility defaults,
- user safety preferences (cannot bypass context floor).

### 9.2 Policy activation
A policy is active if:
- referenced by the active ContextDeclaration for the Lens,
- adopted/active per receipts/outcomes,
- locally available and hash-verified.

Fail-closed recommendation:
- in M2/M3 contexts, if a required policy is missing/unverifiable, default discovery SHOULD degrade to `METADATA_ONLY` or refuse.

---

## 10. Discovery pipeline (search) (normative)

### 10.1 Pipeline stages
1) Candidate generation (IndexProvider)
2) Safety gating (hard)
3) PVL/validity gating (hard; policy-controlled)
4) Ranking/scoring (soft)
5) Diversity/caps (soft)
6) Explanations + provenance (required)

### 10.2 Safety buckets
Each result MUST have one bucket:
- `ALLOW`
- `WARN`
- `BLUR`
- `METADATA_ONLY`
- `BLOCK`

### 10.3 Ranking transparency
Ranking MUST:
- use only features allowed by RankingPolicy,
- include “why” explanations,
- include policy hashes and index snapshot identity (if used).

Ranking MUST also produce a stable total order:
- ties MUST be broken deterministically (as specified by RankingPolicy),
- and the final tie-breaker SHOULD be `artifact_hash` ascending.

---

## 11. Projection pipeline (pages) (normative)

NEXUS MUST support online pages built as projections from the semantic graph.

### 11.1 Projection kinds (minimum)
- `meaning_page` (dictionary)
- `entity_page` (encyclopedia)
- `event_page` (event cluster)
- `digest_page` (time-bounded updates / “news digest”)

### 11.2 Projection phases
1) **Bounded selection** under Lens + ProjectionPolicy:
   - claims,
   - relevant governance outcomes (e.g., contestation),
   - evidence summaries,
   - pack entries for labels/relations.
   - selection MUST be deterministic; ties in “importance” or eligibility MUST be broken deterministically (final tie-breaker SHOULD be `claim_hash` ascending).
2) **Rendering**:
   - deterministic via PSR (REQUIRED),
   - optional AI renderer (OPTIONAL) if enabled and verified.

### 11.3 PSR integration (required)
NEXUS MUST generate a PSR `RenderBundle` per PSR v0.1 including:
- per-claim citations (claim hashes),
- status markers (lane + contested/attested),
- neutrality constraints enabled by default,
- all labels needed for rendering (or allow PSR fallback rules).

### 11.4 EvidenceView / source excerpt view (recommended)
If the source text is available as evidence, NEXUS SHOULD provide an **EvidenceView** derived output that:
- displays the exact excerpt referenced by the claim’s selector(s),
- is explicitly tagged as evidence (not narrative),
- is safety-gated (same buckets),
- never autoplays or autoexecutes payloads.

EvidenceViews MAY be embedded as expandable blocks in projections, but MUST be policy-controlled (SafetyPolicy + ProjectionPolicy).

### 11.5 ProjectionSnapshot (recommended)
For public pages, NEXUS SHOULD produce a ProjectionSnapshot containing:
- projection kind + target id,
- lens hash (contexts + policies + editions),
- selected claim hashes,
- truncation/budget hits,
- renderer identity (PSR template library hash, PSR version; AI model id if used),
- output hash.

---

## 12. Ingestion pipeline (text → candidates) 

NEXUS is intended to grow from user-authored text and reports. Therefore, NEXUS MUST support ingestion workflows in which text becomes candidate claims through PSA.

### 12.1 Ingestion phases
1) **Evidence anchoring** (canonical):
   - store the source bytes as an EvidenceSubstrate artifact (content-addressed),
   - record capture metadata and visibility/encryption.
2) **PSA extraction** (derived; may be persisted as non-claim PRISM artifacts for audit):
   - run PSA on the evidence substrate,
   - produce a CandidateBundle containing:
     - candidate claims,
     - entity candidates,
     - meaning mapping suggestions,
     - selectors back to the evidence substrate,
     - method identity (code hash/model id),
     - routing recommendation (proposed/quarantine).
3) **Review and publishing** (governed):
   - present candidates in a review UI,
   - accepted candidates are converted into PRISM Claim artifacts,
   - new claims enter `proposed` or `quarantine` lanes by default,
   - governance receipts/outcomes (or edition inclusion) admit content to shared lanes.

### 12.2 PSA integration (required hook)
A conforming NEXUS implementation MUST be able to:
- accept a PSAInputBundle (or equivalent config),
- execute PSA (deterministic or model-assisted),
- store and retrieve CandidateBundles for review.

NEXUS MUST treat CandidateBundles as **non-authoritative** and MUST NOT:
- auto-admit their contents,
- treat their confidence scores as truth.

### 12.3 CandidateBundle storage (recommended)
NEXUS SHOULD store CandidateBundles in a content-addressed way:
- either as PRISM artifacts (`artifact_type = "CandidateBundle"`) or
- as derived records keyed by a content hash.

If stored as PRISM artifacts, they MUST be clearly labeled as:
- `artifact_type = "CandidateBundle"` (non-claim),
- with method identity and nondeterminism declarations.

### 12.4 Conversion: CandidateClaim → Claim (normative rule)
Conversion from CandidateClaims to PRISM Claim artifacts MUST:
- preserve evidence selectors back to the EvidenceSubstrate,
- preserve uncertainty/modality where possible (qualifiers),
- record the publishing method as a Derivation (recommended),
- obey context pack allowlists and PVL floors if publishing into governance-visible lanes.

### 12.5 Default routing (normative)
Automatically extracted candidates MUST default to:
- `proposed` or `quarantine`,
- and MUST NOT be default-indexed for public discovery unless policy explicitly allows opt-in lanes.

---

## 13. Policies executed by NEXUS (overview)

NEXUS executes policy artifacts (PRISM artifacts) including:
- `SafetyPolicy`
- `RankingPolicy`
- `QualityPolicy`
- `AdmissionPolicy`
- `ServicePolicy`
- `IndexPolicy`
- `ProjectionPolicy`
- `IngestionPolicy` — recommended when PSA is used

Normative policy artifact schemas are defined in `nexus/NEXUS-POLICIES.md`.

All policies SHOULD share a common header (`policy_id`, `policy_version`, `policy_kind`, `applies_to`, `effective_from`, `supersedes`).

---

## 14. IngestionPolicy (normative; schema in NEXUS-POLICIES)

### 14.1 Purpose
IngestionPolicy governs:
- whether PSA is allowed in a context,
- which PSA modes/tools/models are permitted,
- pack allowlists for meaning supply chain control,
- candidate routing defaults (proposed/quarantine),
- privacy minimization rules (no raw text dumping),
- thresholds for auto-conversion into Claim artifacts (if ever allowed).

### 14.2 Minimum body schema (example)
```json
{
  "policy_id": "ingestion-default",
  "policy_version": "1.0",
  "policy_kind": "IngestionPolicy",
  "description": { "text": "PSA ingestion rules.", "lang": "en" },

  "applies_to": { "contexts": ["weave:context:..."], "content_classes": ["claims","media","pii_directory"] },

  "psa": {
    "enabled": true,
    "allowed_modes": ["deterministic","model_assisted"],
    "allowed_tools": [{ "tool_identity": "psa:tool:default", "required_code_hash": "sha256:..." }],
    "allowed_models": [{ "model_id": "optional", "constraints": { "temperature_max": 0.2 } }]
  },

  "meaning_supply_chain": {
    "pack_allowlist": ["sha256:..."],
    "predicate_taxonomy_pack": "sha256:..."
  },

  "output_controls": {
    "max_candidates": 500,
    "require_selectors": true,
    "max_excerpt_chars": 400,
    "prefer_attribution_when_ambiguous": true
  },

  "routing": {
    "default_lane": "proposed",
    "pii_default_lane": "quarantine"
  },

  "auto_publish": {
    "enabled": false,
    "allowed_statement_types": [],
    "require_human_review": true
  }
}
```

### 14.3 Execution rules (normative)
- If `psa.enabled == false`, PSA runs MUST be refused for that context in default lenses.
- Pack allowlists MUST be enforced during meaning mapping and during candidate→claim conversion.
- Automatic extraction MUST route to proposed/quarantine by default.
- If auto-publish exists at all, it MUST remain policy-controlled, logged, and lane-limited (never directly to admitted/featured).

---

## 15. APIs (conceptual)

NEXUS MAY expose HTTP APIs, local RPC APIs, or library APIs.

### 15.1 Canonical artifact APIs
- `GET /artifacts/{hash}`
- `POST /publish` (submit artifact; policies may gate)
- `GET /packs/{hash}`
- `GET /contexts/{context_id}` (active declaration + hash)

### 15.2 Graph query APIs (derived)
- `POST /query/claims`
- `POST /query/neighbors`
- `POST /query/evidence_for_claim`
- `GET /status/{context_id}/{artifact_hash}`

### 15.3 Search API (derived)
- `POST /search` → results with safety buckets, PVL summaries, ranking explanations, provenance

### 15.4 Projection/page API (derived)
- `GET /page/meaning/{pack_hash}/{entry_uid}?lens=...`
- `GET /page/entity/{instance_id}?lens=...`
- `GET /page/event/{instance_id}?lens=...`
- `GET /page/digest?since=...&until=...&lens=...`

Responses SHOULD include:
- rendered bytes (html/markdown),
- structured blocks (recommended),
- provenance (lens hash, policy hashes, editions, ProjectionSnapshot id if available),
- citations mapping to claim hashes.

### 15.5 EvidenceView API (derived; recommended)
- `GET /evidence/view?substrate={hash}&selector={...}&lens=...`
Returns:
- excerpt bytes or safe placeholder,
- safety bucket,
- provenance (claim hashes that reference it, where applicable).

### 15.6 Ingestion APIs
- `POST /ingest/text`
  - body: bytes or reference
  - returns: EvidenceSubstrate hash
- `POST /extract/psa`
  - body: PSAInputBundle
  - returns: CandidateBundle id/hash
- `GET /candidates/{id}`
  - returns: CandidateBundle
- `POST /candidates/{id}/publish`
  - converts selected CandidateClaims into PRISM Claim artifacts and publishes them to a lane (default proposed)
  - MUST record evidence selectors and (recommended) a Derivation record of the conversion
  - MUST obey AdmissionPolicy and context floors
  - in Human Commons contexts, MUST require an accountable human principal authorization/signature for any resulting governance-visible Claim publication (per TCA/WEAVE)

---

## 16. Privacy, encryption, and “can’t be unseen”

NEXUS MUST:
- prevent encrypted/sensitive payloads from leaking into public/federated derived indexes,
- apply safety gating to previews and evidence excerpts,
- treat executables as metadata-only unless policy permits further access,
- respect suppression outcomes and restricted visibility in projections and ingestion review.
- when automated agents access sensitive/delicate/can’t-be-unseen content, require a Sealed Access Dossier (SAD) or equivalent governed authorization (per TCA/WEAVE) where policy mandates it.

---

## 17. Import/export, editions, and forkability

NEXUS SHOULD support Edition import/export:
- include artifact hashes, pack dependencies, governance artifacts needed to reconstruct lanes/status,
- preserve integrity proofs (hashes/signatures),
- include policy hashes and context declaration hashes used for replay.

---

## 18. Conformance checklist

A NEXUS implementation is conformant to v1.0 if it:

1) Stores PRISM artifacts by hash and preserves canonical bytes.  
2) Resolves MeaningRefs to verified packs for PVL-2+ interpretation.  
3) Implements PVL validation reporting: declared vs validated PVL with explainable failures.  
4) Materializes a queryable claim graph with qualifiers, evidence summaries, and governance status.  
5) Implements discovery pipeline: candidate generation → safety gating → PVL gating → ranking → explanations.  
6) Implements projections with deterministic PSR rendering and provenance.  
7) Provides EvidenceView (or equivalent) safely gated to show source excerpts (recommended).  
8) Implements PSA ingestion hooks producing CandidateBundles anchored to evidence selectors (required hook).  
9) Defaults PSA outputs to proposed/quarantine and prevents default indexing of auto-extracted content.  
10) Derives lane/status strictly from receipts/outcomes and editions (no mutable flags).  
11) Enforces authorship constraints required by the active context/governance profile (e.g., Human Commons canonical Claim publications require an accountable human principal authorization/signature).  

---

## Appendix A — IndexSnapshot and replay records (informative)

If a context requires replayability, NEXUS SHOULD record:
- IndexSnapshot (provider identity, code hash, params, inputs)
- ReplayRecord (query hash, lens hash, snapshot hash, result list hash)

Replay windows and nondeterminism MUST be declared.

Recommended determinism rule:
- `result_list_hash` SHOULD be computed over the **ordered** result list after applying RankingPolicy tie-breakers (final tie-breaker `artifact_hash` ascending), so the same replay inputs yield the same list hash within the declared window.

---

## Appendix B — ProjectionSnapshot (informative)

A ProjectionSnapshot SHOULD record:
- target, kind
- lens hash (contexts + policies + editions)
- selected claim hashes
- truncations and budgets hit
- renderer identity (PSR template library hash, PSR version; AI model id if used)
- output hash

---

## Appendix C — CandidateBundle as artifact (informative)

If storing CandidateBundles as PRISM artifacts is desired:
- use `artifact_type = "CandidateBundle"`
- include `method` identity and nondeterminism declaration
- ensure bundles are clearly non-claims and routed to review lanes by policy
