# PSA v0.1 — PRISM Semantic Analyzer Specification
SPDX-FileCopyrightText: © 2025–present Victor M. Barrientos <firmw.guy@gmail.com>
SPDX-License-Identifier: CC-BY-SA-4.0
**Status:** Draft for implementation  
**Last updated:** 2025-12-26  
**Scope:** Converting human-authored text (reports, writings, messages) into **candidate semantic structures** (candidate claims, entity candidates, meaning mapping suggestions) suitable for insertion into NEXUS review lanes.

---

## 0. Purpose

PSA (PRISM Semantic Analyzer) is the conceptual opposite of PSR:

- **PSR:** structured semantics → surface language (deterministic rendering)
- **PSA:** surface language → structured semantics (**candidate extraction**)

PSA is designed to help users and communities:
- ingest text into a PRISM/NEXUS knowledgebase,
- fragment narrative into atomic **candidate claims**,
- attach **precise evidence selectors** back to the original text,
- propose meaning/predicate mappings against PRISM packs,
- propose entity resolution matches against existing instances,
- and route outputs into **governed review lanes** (proposed/quarantine) for admission.

PSA is intentionally **non-authoritative**:
- it does not decide truth,
- it does not admit content,
- it does not create canonical facts.
It only produces **candidates** with provenance, confidence, and alternatives.

---

## 1. Non-goals (normative)

PSA MUST NOT:
- decide what is true,
- publish admitted/featured content or bypass governance lanes,
- silently collapse uncertainty into certainty,
- silently resolve ambiguous meanings or entity identity,
- fetch external data (web browsing, third-party retrieval) unless explicitly provided as an input artifact and permitted by policy,
- output “final” PRISM Claim artifacts as admitted content.

PSA MAY:
- propose candidate mappings with confidence scores,
- generate multiple interpretations for ambiguous text,
- output preview bundles for rendering (optional),
- output warnings (PII detected, ambiguous reference, quoted speech).

---

## 2. Position in the stack

PSA typically runs inside **NEXUS ingestion**, but may also run locally in a client.

- **PRISM** defines canonical artifacts (Claims, Packs, EvidenceSubstrate, Selectors, Derivations, PVL).
- **NEXUS** stores artifacts, executes policies, builds graphs, runs projections (PSR).
- **PSA** consumes an evidence substrate (the text) and emits structured candidates.
- **WEAVE / WKGC** govern how (and whether) candidates become admitted/shared memory.

> PSA outputs must enter **proposal lanes** by default. Admission remains a governance act.

---

## 3. High-level architecture

### 3.1 Core pipeline (conceptual)
A PSA run typically performs:

1) **Ingest anchor**: ensure the source text exists as an EvidenceSubstrate (content-addressed).  
2) **Segmentation**: sentence/clause segmentation with span offsets.  
3) **Statement detection**: identify candidate propositional units and classify type.  
4) **Argument extraction**: propose subject/predicate/object and qualifiers.  
5) **Meaning mapping**: propose predicate MeaningRefs and qualifier MeaningRefs.  
6) **Entity extraction & resolution**: propose instance candidates and match suggestions.  
7) **Evidence linking**: attach TextSpanSelectors to each candidate claim.  
8) **Candidate packaging**: emit CandidateBundles with confidence, alternatives, warnings.  
9) **Routing**: deliver candidates to NEXUS proposed/quarantine lanes for review.

### 3.2 “No authority” contract (normative)
PSA MUST output **candidates**, not canonical claims:
- The output is intended for review, editing, splitting/merging, and governance routing.
- A separate publishing step (human or policy-controlled) may convert accepted candidates into PRISM Claim artifacts.

---

## 4. Core concepts

### 4.1 Evidence-first extraction
All extracted candidates MUST reference the original source text via:
- the **EvidenceSubstrate hash** of the source bytes, and
- one or more **TextSpanSelectors** (or equivalent selector type) pointing to the exact spans.

This enables audit: “What in the source justified this candidate?”

### 4.2 Candidate vs Claim
- **Claim (PRISM):** canonical, immutable, hash-addressed assertion artifact.
- **CandidateClaim (PSA):** mutable suggestion with confidence and alternatives.

A CandidateClaim is not an assertion; it is a *proposal* to create an assertion.

### 4.3 Meaning mapping suggestions
PSA may propose:
- predicate MeaningRef(s),
- qualifier MeaningRef(s),
- entity type MeaningRef(s),
but MUST preserve ambiguity rather than forcing a single mapping silently.

### 4.4 Entity resolution suggestions
PSA may propose:
- existing instance_id matches,
- new instance proposals,
- “same_as candidate” links,
but MUST provide evidence/rationale and confidence.

---

## 5. PSA inputs and outputs (normative)

### 5.1 PSAInputBundle
A PSA run MUST accept a single input bundle object:

```json
{
  "psa_version": "0.1",
  "job_id": "uuid-or-hash",
  "requested_at": "2025-12-26T00:00:00Z",

  "lens": {
    "contexts": ["weave:context:..."],
    "policies": {
      "ingestion_policy": "sha256:...",    // optional, recommended
      "safety_policy": "sha256:...",       // optional, recommended
      "quality_policy": "sha256:...",      // optional
      "admission_policy": "sha256:...",    // optional
      "projection_policy": "sha256:..."    // optional
    }
  },

  "source": {
    "evidence_substrate_hash": "sha256:...",
    "media_type": "text/plain",
    "language": "en",
    "encoding": "utf-8"
  },

  "packs": {
    "meaning_pack_allowlist": ["sha256:..."],
    "predicate_taxonomy_pack": "sha256:...",   // optional
    "pii_taxonomy_pack": "sha256:..."          // optional
  },

  "entity_resolution": {
    "enabled": true,
    "candidate_scopes": ["local","context","federated"],
    "max_entity_candidates": 5
  },

  "extraction": {
    "mode": "deterministic | model_assisted",
    "max_candidates": 500,
    "min_confidence": 0.0,
    "preserve_quotes": true,
    "prefer_attribution_when_ambiguous": true
  },

  "outputs": {
    "include_sentence_segments": true,
    "include_normalized_text": true,
    "include_alternatives": true
  }
}
```

#### Normative notes
- PSA MUST NOT require policies/lens to run, but if present it SHOULD respect them (especially safety/PII constraints).
- `meaning_pack_allowlist` SHOULD be enforced to avoid “meaning supply chain” surprises.

### 5.2 PSAOutputBundle (CandidateBundle)
PSA MUST output a single CandidateBundle object:

```json
{
  "psa_version": "0.1",
  "job_id": "uuid-or-hash",
  "completed_at": "2025-12-26T00:01:23Z",

  "source": {
    "evidence_substrate_hash": "sha256:...",
    "media_type": "text/plain",
    "language": "en"
  },

  "method": {
    "mode": "deterministic | model_assisted",
    "tool_identity": "psa:tool:...",

    "code_hash": "sha256:...",              // required for deterministic mode
    "model_id": "optional-model-id",        // required for model_assisted mode
    "nondeterminism": {
      "declared": true,
      "notes": "temperature=0.2; sampling; or 'none' for deterministic"
    }
  },

  "segments": [ /* optional; sentence/clause segmentation */ ],
  "candidates": [ /* CandidateClaim objects */ ],
  "entity_candidates": [ /* CandidateEntity objects */ ],
  "meaning_suggestions": [ /* CandidateMeaningMapping objects */ ],

  "warnings": [ /* PII, ambiguity, unsupported language, etc. */ ],

  "routing": {
    "recommended_lane": "proposed | quarantine",
    "reason": "auto_extraction_requires_review"
  }
}
```

#### Normative notes
- PSA MUST include `method` with enough identity to audit and replay where possible.
- PSA MUST include `routing.recommended_lane` defaulting to `proposed` or `quarantine` for automatic extraction.

---

## 6. Segmentation output (optional but recommended)

If `include_sentence_segments == true`, PSA SHOULD emit segments:

```json
{
  "segment_id": "s1",
  "kind": "sentence | clause",
  "text": "Paris is located in France.",
  "selector": {
    "selector_type": "TextSpanSelector",
    "start": 120,
    "end": 147
  }
}
```

Rules:
- Offsets MUST be byte offsets over UTF-8 bytes **or** Unicode codepoint offsets, but the bundle MUST declare which.
- Selectors MUST be precise enough to highlight the original substring.

---

## 7. CandidateClaim format (normative)

A CandidateClaim represents one proposed atomic assertion.

### 7.1 Required fields (minimum)
```json
{
  "candidate_id": "c1",
  "statement_type": "assertion | attribution | measurement | event | definition | relation | other",

  "source_selectors": [
    { "selector_type": "TextSpanSelector", "start": 120, "end": 147 }
  ],

  "surface_text": "Paris is located in France.",
  "normalized_text": "Paris located_in France.",

  "proposed": {
    "subject": { "kind": "entity_ref", "text": "Paris" },
    "predicate": { "kind": "predicate_text", "text": "located in" },
    "object": { "kind": "entity_ref", "text": "France" }
  },

  "confidence": {
    "overall": 0.82,
    "subject": 0.80,
    "predicate": 0.75,
    "object": 0.85
  },

  "alternatives": {
    "predicate_meanings": [
      { "meaning_ref": { "pack_hash": "sha256:...", "entry_uid": "uid:pred:located_in" }, "confidence": 0.72 }
    ],
    "subject_entities": [
      { "instance_id": "inst:uuid:...", "confidence": 0.66, "match_basis": "label_exact" }
    ],
    "object_entities": [
      { "instance_id": "inst:uuid:...", "confidence": 0.77, "match_basis": "alias_match" }
    ]
  },

  "qualifiers": [
    {
      "kind": "time_as_of",
      "value": { "datatype": "date", "value": "2025-12-26" },
      "confidence": 0.55
    }
  ],

  "polarity": "affirm | deny | unknown",
  "modality": "certain | probable | possible | quoted | reported | unknown",

  "notes": { "text": "Optional explanation for reviewers." }
}
```

### 7.2 Evidence linking rule (normative)
Each CandidateClaim MUST include at least one `source_selector` and MUST inherit the `evidence_substrate_hash` from the bundle source.

A later “candidate → claim” publisher MUST be able to construct a PRISM evidence chain:
- EvidenceSubstrate (source hash) → TextSpanSelector → (optional) Derivation → Support assertion.

### 7.3 Attribution vs assertion (normative guidance)
If the source sentence is ambiguous, PSA SHOULD prefer safe forms:
- **Attribution claim**: “Document D asserts X” or “Person P said X”
over:
- **Direct world assertion**: “X”

This reduces over-assertion and supports audit.

### 7.4 Claim splitting and merging
PSA MAY output:
- a single CandidateClaim for a compound sentence, OR
- multiple CandidateClaims with shared selectors.

If compound, PSA SHOULD set:
- `statement_type = relation` or `other`,
and include a warning suggesting manual split.

---

## 8. CandidateEntity format (recommended)

Entity candidates represent extracted entities and possible resolutions.

```json
{
  "entity_candidate_id": "e1",
  "surface_text": "Paris",
  "source_selectors": [{ "selector_type": "TextSpanSelector", "start": 120, "end": 125 }],

  "entity_type_suggestions": [
    { "meaning_ref": { "pack_hash": "sha256:...", "entry_uid": "uid:type:city" }, "confidence": 0.40 }
  ],

  "resolution": {
    "matches": [
      { "instance_id": "inst:uuid:...", "confidence": 0.66, "match_basis": "label_exact" }
    ],
    "propose_new_instance": true
  }
}
```

Normative notes:
- PSA MUST NOT silently choose a match when confidence is low.
- PSA SHOULD provide match bases (label match, alias match, local graph proximity, etc.) to support review.

---

## 9. CandidateMeaningMapping format (optional)

Meaning mapping candidates propose how surface tokens correspond to meaning entries.

```json
{
  "mapping_id": "m1",
  "surface_text": "located in",
  "role": "predicate",

  "candidates": [
    { "meaning_ref": { "pack_hash": "sha256:...", "entry_uid": "uid:pred:located_in" }, "confidence": 0.72 },
    { "meaning_ref": { "pack_hash": "sha256:...", "entry_uid": "uid:pred:in_region" }, "confidence": 0.21 }
  ],

  "notes": { "text": "Ambiguous mapping; reviewer should select." }
}
```

If no suitable meaning exists:
- PSA MAY emit a “meaning proposal suggestion” for pack authorship, but MUST NOT publish it as canonical.

---

## 10. Method identity, determinism, and replay (normative)

### 10.1 Deterministic mode requirements
If `mode == deterministic`, PSA MUST include:
- `code_hash` identifying the extraction code,
- stable ruleset version ids for any pattern libraries,
- stable tokenizer/segmenter version.

Given the same input bundle and same tool version, outputs MUST be byte-identical (ignoring timestamps).

### 10.2 Model-assisted mode requirements
If `mode == model_assisted`, PSA MUST include:
- `model_id`,
- declared nondeterminism sources,
- parameters that materially affect output (temperature, top_p, etc.) when applicable,
- a validator step that ensures the output schema is well-formed.

Replayability MAY be limited; PSA MUST be honest about that via `method.nondeterminism`.

### 10.3 Derivation recording (recommended)
PSA runs SHOULD be recorded as PRISM `Derivation` artifacts:
- input: EvidenceSubstrate hash
- outputs: CandidateBundle hash (or embedded bytes)
- method identity: code hash / model id

This enables audit and (where possible) reproduction.

---

## 11. Safety, PII, and “can’t be unseen” (normative)

PSA extraction can inadvertently surface sensitive content.

### 11.1 PII detection (recommended)
If a PII taxonomy pack is provided, PSA SHOULD:
- classify candidate segments as potential PII,
- emit warnings, and
- recommend routing to `quarantine` or restricted visibility lanes.

### 11.2 Output minimization (required concept)
PSA SHOULD avoid copying large raw text into the CandidateBundle.
Prefer:
- selectors + short excerpts (bounded length),
- with “view evidence excerpt” handled by NEXUS under SafetyPolicy.

### 11.3 No automatic dissemination
CandidateBundles produced automatically MUST default to:
- proposed/quarantine lanes,
- and MUST NOT be default-indexed for public discovery.

---

## 12. Internationalization and language handling

- PSA SHOULD support BCP-47 language tags.
- If language is unsupported, PSA MUST:
  - emit a warning,
  - fall back to minimal segmentation (or none),
  - and may output “unknown mapping” candidates rather than hallucinating meanings.

PSA MUST NOT silently machine-translate via external services.

---

## 13. Extensibility

PSA may be extended with:
- new statement types,
- new selector types (for PDFs/audio/video; time span selectors, region selectors),
- domain-specific predicate mapping packs,
- richer coreference resolution (still candidate-based).

Unknown extensions MUST fail closed or degrade gracefully:
- do not output malformed candidates.

---

## 14. Conformance checklist (PSA v0.1)

A PSA implementation is conformant if it:

1) Accepts PSAInputBundle and produces PSAOutputBundle.  
2) Anchors every CandidateClaim to an EvidenceSubstrate hash and at least one selector.  
3) Produces candidates only (no direct admission; no silent truth claims).  
4) Provides method identity (code hash or model id) and declares nondeterminism.  
5) Preserves ambiguity via alternatives rather than forced mappings when uncertain.  
6) Defaults routing to proposed/quarantine for automatic extraction.  
7) Does not fetch external data unless explicitly provided as input artifacts and policy permits.  

---

# Appendix A — Recommended statement types (informative)

- `assertion`: direct propositional claim (“X is Y”)
- `attribution`: speech/report claim (“P said X”, “Document D states X”)
- `measurement`: numeric claim with units (“X is 10 kg”)
- `event`: occurrence with time (“X happened on date D”)
- `relation`: structural relations (“X is part of Y”)
- `definition`: definitional text (“X means Y”) — often maps to pack authoring rather than world facts
- `other`: fallback

---

# Appendix B — Selector recommendations (informative)

For text substrates:
- `TextSpanSelector` (byte offsets) is recommended.

For rich media (future extension):
- `TimeSpanSelector` for audio/video
- `RegionSelector` for images/PDF pages
- `CompositeSelector` for multi-part evidence

---

# Appendix C — Why PSA is a separate spec (informative)

PSA is high-risk compared to PSR:
- PSA can propose incorrect claims and mappings.
- PSA requires review lanes and safety routing.
- PSA may use nondeterministic models and must declare that.

Keeping PSA separate makes conformance, auditing, and governance controls clearer.
