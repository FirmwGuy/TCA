# NEXUS-POLICIES v0.1 — Policy Artifact Schemas for NEXUS
SPDX-FileCopyrightText: © 2025–present Victor M. Barrientos <firmw.guy@gmail.com>
SPDX-License-Identifier: CC-BY-SA-4.0
**Status:** Draft for implementation  
**Last updated:** 2025-12-27  
**Companion specs:** PRISM v1.0, NEXUS v1.0, WEAVE (Constitution + WCIC), WKGC v0.1, WCL v0.1

This document defines the **normative, machine-executable policy artifact schemas** referenced by NEXUS and WEAVE/WKGC contexts.

Policies are **not governance**: governance legitimacy is established by ContextDeclarations plus receipts/outcomes. Policies are **deterministic enforcement/configuration artifacts** that NEXUS executes.

---

## 0. Purpose

NEXUS executes policies to:
- gate exposure (safety),
- enforce verifiability floors (quality),
- rank and explain results (ranking),
- control admission/publishing pathways (admission),
- enforce influence budgets and service constraints (service),
- constrain index providers and replay requirements (index),
- constrain projection selection and rendering (projection),
- constrain ingestion and PSA usage (ingestion).

This spec exists so multiple implementations can interoperate and publish conformance evidence.

---

## 1. Conformance language

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in RFC 2119.

---

## 2. Policy artifacts in PRISM terms (normative)

### 2.1 Artifact type model

In this stack, each policy kind is represented as a **PRISM extension artifact type**:
- `artifact_type = "SafetyPolicy"`
- `artifact_type = "QualityPolicy"`
- `artifact_type = "RankingPolicy"`
- `artifact_type = "AdmissionPolicy"`
- `artifact_type = "ServicePolicy"`
- `artifact_type = "IndexPolicy"`
- `artifact_type = "ProjectionPolicy"`
- `artifact_type = "IngestionPolicy"`

Policy artifacts:
- MUST be valid PRISM artifacts (envelope + typed body).
- MUST be immutable once published. Updates are new artifacts that may supersede prior ones.
- MUST be content-addressed by the PRISM hashing rules.

### 2.2 Common policy header (required)

Each policy artifact `body` MUST include the following header fields:

```json
{
  "policy_id": "human-friendly-id",
  "policy_version": "semver-or-string",
  "policy_kind": "SafetyPolicy | QualityPolicy | RankingPolicy | AdmissionPolicy | ServicePolicy | IndexPolicy | ProjectionPolicy | IngestionPolicy",
  "description": { "text": "…", "lang": "en" },

  "applies_to": {
    "contexts": ["weave:context:..."],
    "content_classes": ["claims","media","software","pii_directory","dataset","other"]
  },

  "effective_from": "2025-12-26T00:00:00Z",
  "supersedes": ["sha256:..."]
}
```

Rules:
- `policy_id` is human-friendly; **identity is the artifact hash**.
- `policy_kind` MUST equal the PRISM envelope `artifact_type`.
- `effective_from` is OPTIONAL; when omitted, the policy is effective immediately once adopted by governance.
- `supersedes` is OPTIONAL; when present it SHOULD list prior policy artifact hashes superseded by this one.

### 2.3 Determinism and fail-closed (required concept)

Policy evaluation that affects:
- visibility/exposure,
- admission/state transitions,
- public discovery ranking,
- safety gating,
- or audit outputs,
MUST be deterministic **or** decision-recorded with replay artifacts per context requirements.

Fail-closed recommendation (especially M2/M3 contexts):
- if a policy required by a ContextDeclaration is missing or unverifiable, NEXUS SHOULD degrade to `METADATA_ONLY` or refuse the operation (publish/search/projection) rather than silently proceeding.

---

## 3. Policy kinds (schemas)

All schemas below define the required/optional fields inside the policy artifact `body`, in addition to the common header above.

### 3.1 Safety buckets (shared enum)

Where policies refer to an exposure bucket, the allowed values are:
- `ALLOW`
- `WARN`
- `BLUR`
- `METADATA_ONLY`
- `BLOCK`

Buckets MUST be applied in the pipeline order required by TCA/NEXUS: safety gating precedes PVL gating precedes ranking.

---

## 4. SafetyPolicy (normative)

**Purpose:** decide whether an artifact/result/evidence excerpt may be shown, and at what level of exposure.

Minimum required fields:

```json
{
  "risk_taxonomy_pack": "sha256:...",

  "defaults": {
    "bucket": "WARN",
    "executables_bucket": "METADATA_ONLY",
    "unknown_media_bucket": "BLUR"
  },

  "content_class_rules": {
    "claims": { "default_bucket": "ALLOW" },
    "media": { "default_bucket": "WARN" },
    "software": { "default_bucket": "METADATA_ONLY" },
    "pii_directory": { "default_bucket": "METADATA_ONLY" }
  },

  "risk_rules": [
    {
      "risk_meaning_refs": [{ "pack_hash": "sha256:...", "entry_uid": "uid:risk:graphic_violence" }],
      "bucket": "BLUR"
    }
  ],

  "preview_controls": {
    "autoplay_media": false,
    "allow_inline_excerpts": true,
    "max_excerpt_chars": 400
  },

  "pii_controls": {
    "pii_taxonomy_pack": "sha256:...",
    "public_indexing": "forbidden",
    "default_bucket": "METADATA_ONLY"
  }
}
```

Rules:
- `risk_taxonomy_pack` MUST be a pack hash that defines the risk meanings referenced by this policy.
- Executable payloads MUST default to `METADATA_ONLY` unless an explicit policy rule allows more (and a scanning/sandboxing regime is in place).
- For “can’t-be-unseen” categories, policies SHOULD prefer `BLUR`/`METADATA_ONLY` before `ALLOW`.

---

## 5. QualityPolicy (normative)

**Purpose:** enforce PVL/validity floors and evidence requirements for inclusion and for admission actions.

Minimum required fields:

```json
{
  "pvl": {
    "floor_default": 0,
    "floor_by_content_class": { "claims": 2, "media": 1, "software": 2, "pii_directory": 2 }
  },

  "require_validated_pvl": {
    "for_public_discovery": true,
    "for_admission": true
  },

  "evidence": {
    "min_chain_count_default": 1,
    "require_selectors_when_applicable": true,
    "high_impact": {
      "predicate_kind_pack": "sha256:...",
      "predicate_kinds_requiring_stricter_evidence": ["high_impact"],
      "min_chain_count": 2
    }
  }
}
```

Rules:
- PVL floors MUST be enforced at the points they claim to apply (search gating, projection selection, admission).
- If `require_validated_pvl.* == true`, `validated PVL == unknown` MUST be treated as a failure for that pathway.

---

## 6. RankingPolicy (normative)

**Purpose:** define allowed ranking features, tie-breakers, and explanation requirements.

Minimum required fields:

```json
{
  "explanations": { "required": true },

  "features": {
    "allow": ["pvl", "lane", "evidence_strength", "local_popularity", "recency"],
    "forbid": ["global_popularity", "cross_context_reputation"]
  },

  "tie_breakers": [
    "score_desc",
    "pvl_validated_desc",
    "lane_weight_desc",
    "artifact_hash_asc"
  ]
}
```

Rules:
- Ranking MUST use only allowed features and MUST ignore forbidden features.
- Rankings intended to be replayable MUST use deterministic tie-breakers; `artifact_hash_asc` SHOULD be the final tie-breaker.
- Explanations MUST reference the active `RankingPolicy` hash and the index snapshot identity (if an index is used).

---

## 7. ServicePolicy (normative)

**Purpose:** constrain influence actions (endorsements/challenges/admissions/outreach) using budgets and service inputs.

Minimum required fields:

```json
{
  "epoch_seconds": 604800,

  "actions": {
    "endorse": { "max_per_epoch": 50 },
    "challenge": { "max_per_epoch": 20 },
    "outreach": { "max_per_epoch": 10 },
    "admit": { "max_per_epoch": 5 }
  },

  "service": {
    "sources": ["review_work","storage","relay","verification"],
    "notes": { "text": "How service converts to budgets is governance-specific.", "lang": "en" }
  }
}
```

Rules:
- Budget accounting MUST be deterministic given the relevant receipts/outcomes and service ledgers used by the context.
- A context MAY implement richer economics, but MUST preserve the basic “declared budgets” interface required for auditability.

---

## 8. AdmissionPolicy (normative)

**Purpose:** govern publishing/admission pathways and enforce machine-checkable prerequisites.

Minimum required fields:

```json
{
  "default_publish_lane": "proposed",

  "lanes": {
    "proposed": { "allow_direct_publish": true, "min_pvl": 0 },
    "admitted": { "allow_direct_publish": false, "min_pvl": 2 },
    "featured": { "allow_direct_publish": false, "min_pvl": 2 }
  },

  "high_impact": {
    "require_receipts": true,
    "require_templates_pvl3": true
  },

  "authorship": {
    "require_human_principal_for_human_commons": true
  }
}
```

Rules:
- AdmissionPolicy MUST NOT replace receipts/outcomes; it gates which write-paths are allowed and what minimum prerequisites must be satisfied.
- Contexts MUST NOT allow bypass paths that result in content being treated as admitted/featured without the receipts/outcomes required by the context declaration and adopted charters.
- If `require_human_principal_for_human_commons == true`, writes into governance-visible lanes MUST be attributable to an accountable human principal per TCA/WEAVE requirements.

---

## 9. IndexPolicy (normative)

**Purpose:** constrain index providers and replayability requirements, and prevent leakage of sensitive payloads into public/federated indexes.

Minimum required fields:

```json
{
  "providers": {
    "allow": [{ "provider_id": "index:provider:...", "display_name": "Example", "code_hash": "sha256:..." }],
    "deny": []
  },

  "replay": {
    "require_index_snapshot": true,
    "max_snapshot_age_days": 90,
    "replay_window_seconds": 2592000
  },

  "privacy": {
    "forbid_pii_payload_tokenization": true,
    "forbid_restricted_payload_tokenization": true
  }
}
```

Rules:
- Index providers used for default public discovery MUST be disclosed and governed.
- Policies MUST prevent indexing of sensitive plaintext into public/federated indexes when visibility rules forbid it.

---

## 10. ProjectionPolicy (normative)

**Purpose:** constrain projection selection and rendering (page building).

Minimum required fields:

```json
{
  "selection": {
    "max_depth": 2,
    "predicate_allowlist_pack": "sha256:...",
    "max_claims_total": 200,
    "lane_allowlist": ["admitted","featured"]
  },

  "pvl": { "floor_for_inclusion": 2 },

  "rendering": {
    "psr_required": true,
    "psr_template_library_allowlist": ["sha256:..."],
    "ai_renderer": { "enabled": false }
  },

  "neutrality": {
    "neutral_register": true,
    "forbid_recommendations": true
  }
}
```

Rules:
- Projection selection MUST be deterministic given the same lens + policies + editions + canonical artifacts.
- If an AI renderer is enabled, it MUST be policy-controlled, provenance-tagged, citation-bound, and MUST fall back to PSR when constraints fail (per NEXUS).

---

## 11. IngestionPolicy (normative)

**Purpose:** constrain ingestion and PSA usage, meaning supply chain, routing defaults, and any auto-publish behavior.

Minimum required fields:

```json
{
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
    "require_human_review": true,
    "allowed_statement_types": []
  }
}
```

Rules:
- Pack allowlists MUST be enforced during meaning mapping and during candidate→claim conversion.
- Automatic extraction MUST route to proposed/quarantine by default.
- If auto-publish exists at all, it MUST remain policy-controlled, logged, and lane-limited (never directly to admitted/featured).

---

## 12. Conformance checklist (NEXUS-POLICIES v0.1)

A conforming ecosystem/publisher SHOULD be able to:
1) Publish each policy kind as a PRISM artifact with `artifact_type` equal to `policy_kind`.  
2) Provide policy artifacts with the common header and kind-specific required fields.  
3) Execute each policy deterministically (or decision-recorded) and fail closed when required policies are missing.  
4) Produce audit outputs that reference active policy hashes.
