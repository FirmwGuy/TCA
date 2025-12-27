# WEAVE Addendum — Charter Library for PRISM + NEXUS Commons (WCL)
SPDX-FileCopyrightText: © 2025–present Victor M. Barrientos <firmw.guy@gmail.com>
SPDX-License-Identifier: CC-BY-SA-4.0
**Version:** 0.1
**Status:** Draft for implementation; binding when adopted by a Context  
**Companion specs:** WEAVE Constitution + WCIC, PRISM v1.0, NEXUS v1.0, NEXUS-POLICIES v0.1, WKGC v0.1  
**Last updated:** 2025-12-26

---

## 0. Purpose

This addendum defines a **library of recommended WEAVE Charters** for communities operating PRISM+NEXUS knowledge commons.

Charters are **governance contracts**:
- they express rights/duties, due process, accountability, and safety floors,
- they are **forkable**, **adoptable**, and **auditable**,
- they can require that a Context adopt specific **NEXUS-executed policy artifacts** (SafetyPolicy, RankingPolicy, etc.),
- they can require specific **PRISM verifiability floors** (PVL) and templates for high-impact actions.

This addendum exists because many WEAVE contexts share recurring governance needs that are too important to remain informal:
- high-impact domains (health/legal/finance),
- evidence authenticity in an AI media world,
- index provider accountability (avoid search capture),
- identity/roles/key rotation continuity,
- privacy and PII directories,
- fork and secession procedures.

---

## 1. Charter vs policy vs receipt

### 1.1 Charter
A **Charter** is a governance artifact that declares *what must be true* about:
- acceptable process,
- minimum safeguards,
- eligibility and duties,
- auditability and transparency surfaces.

Charters SHOULD be machine-readable enough for **conformance checking**, but they are not “ranking formulas.”

### 1.2 Policy
A **Policy** is a machine-executed artifact body (e.g., SafetyPolicy, RankingPolicy) that NEXUS runs to gate and rank results.

Normative policy artifact schemas are defined in `nexus/NEXUS-POLICIES.md`.

A charter may require specific policy adoption, but does not replace the policy.

### 1.3 Receipt / Outcome
A **Receipt/Outcome** is an immutable governance record:
- “this charter was adopted,”
- “this decision was made,”
- “this content was suppressed,”
- “this index provider was banned,” etc.

Receipts make governance computable, auditable, and forkable.

---

## 2. Adoption and activation

### 2.1 Charter artifact type
Recommended:
- `artifact_type = "Charter"`

A Charter body MUST include:

```json
{
  "charter": {
    "charter_id": "string",
    "charter_version": "0.1",
    "charter_kind": "HighImpact | EvidenceAuthenticity | IndexAccountability | IdentityKeys | PrivacyPII | ForkSecession | Other",
    "name": { "text": "…", "lang": "en" },
    "description": { "text": "…", "lang": "en" },

    "applies_to": {
      "contexts": ["weave:context:..."],
      "content_classes": ["claims","media","software","pii_directory","dataset","other"]
    },

    "requirements": { }
  }
}
```

Normative notes:
- `charter_id` is human-friendly; identity is the artifact hash.
- Charters MUST be immutable once published. Updates are new artifacts that may supersede prior ones.

### 2.2 Charter adoption receipt
Recommended:
- `artifact_type = "CharterAdoptionReceipt"`

Minimum body:

```json
{
  "receipt": {
    "context_id": "weave:context:example",
    "receipt_type": "CharterAdoptionReceipt",
    "charter_hash": "sha256:...",
    "issued_at": "2025-12-26T00:00:00Z",
    "issuer": "did:key:..."
  }
}
```

A charter is **active** in a context iff:
- it is listed in the ContextDeclaration `charters[]` for that context (see WKGC ContextDeclaration minimum schema), AND
- an active CharterAdoptionReceipt exists, AND
- the charter artifact is locally available and hash-verified.

### 2.3 Conformance reporting (recommended)
NEXUS SHOULD be able to generate a **ContextConformanceReport** (derived) stating:
- which charters are active,
- which required policies are present and adopted,
- which required receipts are missing,
- whether PVL floors are being enforced per context declaration and policies.

---

## 3. Minimum charter capability vocabulary

Charters in this addendum use the following common requirement keys.

### 3.1 PVL floors and strictness
- `pvl_floor_default`: integer 0–3
- `pvl_floor_by_content_class`: mapping
- `pvl_floor_by_predicate_kind`: mapping (predicate kinds defined by pack relations)

### 3.2 Required templates (PVL-3)
- `required_templates`: list of `(pack_hash, entry_uid)` template meanings
- `template_allowlist`: allowlist for PVL-3 acts

### 3.3 Required policy adoption (NEXUS-executed)
- `required_policies`: object listing required policy kinds and minimum versions
- `policy_constraints`: rules like “RankingPolicy must forbid global_popularity”

### 3.4 Required receipts/outcomes for high-impact acts
- `high_impact_actions`: list of action identifiers
- `required_receipt_types`: list of receipts/outcomes required

### 3.5 Replayability / indexing transparency
- `require_index_snapshot`: boolean
- `require_code_hash`: boolean
- `replay_window_seconds`: integer
- `index_provider_requirements`: allowlist/denylist, disclosure requirements

### 3.6 Privacy/PII controls
- `pii_taxonomy_pack`: PackRef/hash
- `consent_required`: boolean
- `default_visibility`: tier
- `encryption_required_for_nonlocal`: boolean
- `indexing_constraints`: list (metadata-only, no-preview, etc.)

### 3.7 Fork/export guarantees
- `export_minimum_artifacts`: which artifact classes must be exportable
- `export_manifest_required`: Editions required (recommended)
- `secession_procedure_required`: boolean

---

# 4. Charter HI-1 — High-Impact Domains Charter (recommended)

**Charter kind:** `HighImpact`  
**Use when:** claims can cause real-world harm if wrong or misused (medicine, law, finance, safety engineering, elections administration, etc.).

### 4.1 Goals
- Raise the duty of care for high-impact claims.
- Require deterministic validation for high-impact speech acts.
- Make “I can’t verify this” visible and actionable.
- Reduce scope for subtle search/ranking capture.

### 4.2 Normative requirements (minimum)
A context adopting HI-1 MUST:

1) Set **default PVL floor ≥ 2** for admitted lanes.  
2) Require **PVL-3** for all **high-impact predicates** (as defined by a predicate-kind taxonomy pack or allowlist).  
3) Require **Template validation** (PVL-3) for:
   - medical recommendations, dosages,
   - legal advice assertions,
   - financial advice assertions,
   - safety-critical instructions,
   - any “verdict/commitment” artifact types used by the context.
4) Require evidence chains with:
   - `min_evidence_chain_count ≥ 2` for high-impact predicates, and
   - selectors when applicable (quotes, page references, time ranges).
5) Require replayability for default public indices:
   - IndexSnapshot artifacts, code hashes, declared nondeterminism.
6) Provide fast dispute and escalation pathways:
   - published ombuds contact,
   - published appeal timelines.

### 4.3 Required policy adoption
HI-1 contexts MUST adopt (at minimum):
- SafetyPolicy
- QualityPolicy (with PVL floors and evidence requirements)
- RankingPolicy (with feature allowlist/denylist)
- AdmissionPolicy
- IndexPolicy (with replay requirements)

### 4.4 Suggested machine-readable requirements block
```json
{
  "requirements": {
    "pvl_floor_default": 2,
    "pvl_floor_by_predicate_kind": { "high_impact": 3 },
    "required_templates": [
      { "pack_hash": "sha256:...", "entry_uid": "uid:tpl:medical_claim" }
    ],
    "replay": { "require_index_snapshot": true, "require_code_hash": true, "replay_window_seconds": 2592000 }
  }
}
```

---

# 5. Charter EV-1 — Evidence Authenticity & Provenance Charter (recommended)

**Charter kind:** `EvidenceAuthenticity`  
**Use when:** the community expects adversarial media manipulation, synthetic media, or disputed provenance.

### 5.1 Goals
- Make authenticity claims explicit rather than implicit.
- Standardize “substrate vs derivation” semantics and avoid “pixels prove reality.”
- Require provenance completeness for admitted media and high-stakes evidence.

### 5.2 Normative requirements (minimum)
A context adopting EV-1 MUST:

1) Treat URLs as **locator hints only**; evidence identity is content-addressed.  
2) Require that admitted claims include evidence chains that specify:
   - support_mode,
   - substrate hash,
   - selectors when precision matters,
   - derivations when claims rely on transformations (OCR/transcript/parsing).
3) Define a policy for synthetic media handling:
   - required labeling (self-label and/or reviewer label),
   - contested status mechanics,
   - disclosure constraints (e.g., metadata-only until reviewed).
4) Require provenance completeness for admitted media:
   - license present,
   - capture metadata when available,
   - chain-of-custody attestations where feasible.
5) For PVL-3 contexts: require deterministic derivation pipelines when derivations are used in support, including code hashes.

### 5.3 Required policy adoption
EV-1 contexts MUST adopt:
- QualityPolicy with explicit evidence chain requirements,
- SafetyPolicy with preview constraints for unreviewed media,
- (optionally) a MediaProvenancePolicy if the ecosystem defines one.

### 5.4 Suggested machine-readable requirements block
```json
{
  "requirements": {
    "evidence": {
      "require_content_addressed_substrates": true,
      "require_selectors_when_applicable": true,
      "require_derivation_artifacts_when_used": true
    },
    "synthetic_media": {
      "require_labeling": true,
      "default_lane_for_unverified": "proposed"
    }
  }
}
```

---

# 6. Charter IX-1 — Index Provider Accountability Charter (recommended)

**Charter kind:** `IndexAccountability`  
**Use when:** the community worries about “search capture” (opaque ranking shaping reality).

### 6.1 Goals
- Make index/ranking pipelines auditable and contestable.
- Prevent forbidden features (popularity-as-authority, cross-context reputation laundering).
- Require explainability and replay windows.

### 6.2 Normative requirements (minimum)
A context adopting IX-1 MUST:

1) Require IndexPolicy with:
   - provider allowlist/denylist,
   - snapshot requirements,
   - max snapshot age,
   - replay window declarations.
2) Require RankingPolicy to:
   - declare allowed_features and forbidden_features,
   - require explanations,
   - specify deterministic tie-breakers.
3) Require IndexProvider disclosures:
   - code/container hash,
   - algorithm family,
   - for vector search: model_id + embedding provenance.
4) Provide a governance pathway to:
   - quarantine or ban an index provider (receipt/outcome),
   - roll back to prior snapshots,
   - publish a “ranking incident report” artifact when needed.

### 6.3 Required receipts/outcomes (recommended)
- `IndexProviderApprovalReceipt`
- `IndexProviderBanOutcome`
- `IndexSnapshotAdoptionReceipt`
- `RankingIncidentOutcome` (for major failures)

### 6.4 Suggested machine-readable requirements block
```json
{
  "requirements": {
    "index": {
      "require_index_snapshot": true,
      "require_code_hash": true,
      "max_snapshot_age_days": 90,
      "replay_window_seconds": 2592000
    },
    "ranking": {
      "require_explanations": true,
      "forbid_features": ["global_popularity","cross_context_reputation"]
    }
  }
}
```

---

# 7. Charter ID-1 — Identity, Roles, and Key Rotation Charter (recommended)

**Charter kind:** `IdentityKeys`  
**Use when:** contexts depend on roles (reviewers/moderators/scanners) and need continuity under key compromise/rotation.

### 7.1 Goals
- Ensure governance remains interpretable across key rotation.
- Define a safe response to compromised keys.
- Make role assignment/removal auditable and contestable.

### 7.2 Normative requirements (minimum)
A context adopting ID-1 MUST:

1) Define how roles are assigned, removed, and audited:
   - reviewer eligibility,
   - moderator eligibility,
   - scanner eligibility (for software receipts).
2) Define key rotation procedure:
   - how a DID/key is updated,
   - how continuity is recorded (RotationReceipt).
3) Define key compromise procedure:
   - emergency suspension outcome,
   - re-issuance of high-impact receipts if needed.
4) Require signatures on high-impact receipts and outcomes.
5) Provide transparency:
   - publish current role holders (with privacy-safe identifiers),
   - publish role changes as receipts.

### 7.3 Recommended receipt types
- `RoleGrantReceipt`
- `RoleRevokeReceipt`
- `KeyRotationReceipt`
- `KeyCompromiseOutcome`
- `EmergencySuspensionOutcome`

### 7.4 Suggested machine-readable requirements block
```json
{
  "requirements": {
    "signatures": { "require_for_high_impact": true },
    "roles": { "publish_role_registry": true },
    "keys": { "require_rotation_receipts": true }
  }
}
```

---

# 8. Charter PI-1 — Privacy & PII Directories Charter (recommended)

**Charter kind:** `PrivacyPII`  
**Use when:** storing PII (directories, contact lists, sensitive datasets).

### 8.1 Goals
- Minimize irreversible harm.
- Ensure consent and revocation pathways.
- Prevent PII leakage into public discovery surfaces.
- Provide “can’t be unseen” controls.

### 8.2 Normative requirements (minimum)
A context adopting PI-1 MUST:

1) Adopt a PII taxonomy pack (risk categories and PII classes).  
2) Require **ConsentReceipt** for any non-local sharing of PII.  
3) Require encryption for non-local storage of PII (unless context is local-only).  
4) Default directory visibility to `members_only` or stricter.  
5) For discovery:
   - prohibit tokenization of PII payloads into public/federated indexes,
   - default to metadata-only results unless authorized.
6) Provide subject access + revocation process:
   - how to request disclosure,
   - how to request suppression,
   - how revocations propagate (best-effort).

### 8.3 Required policy adoption
PI-1 contexts MUST adopt:
- SafetyPolicy with PII gates,
- QualityPolicy with consent requirements,
- AdmissionPolicy with directory-specific checks.

### 8.4 Suggested machine-readable requirements block
```json
{
  "requirements": {
    "pii": {
      "consent_required": true,
      "encryption_required_for_nonlocal": true,
      "default_visibility": "members_only",
      "public_indexing": "forbidden"
    }
  }
}
```

---

# 9. Charter FK-1 — Fork & Secession Charter (recommended)

**Charter kind:** `ForkSecession`  
**Use when:** the community values credible exit and wants to prevent “hostage-taking” of shared memory.

### 9.1 Goals
- Ensure forkability is real, not rhetorical.
- Define how exports happen and what must be included.
- Provide a clean procedure for secession while preserving accountability.

### 9.2 Normative requirements (minimum)
A context adopting FK-1 MUST:

1) Provide an export mechanism that includes:
   - admitted/featured claim artifacts,
   - referenced packs (or pack dependencies),
   - receipts/outcomes needed to reconstruct lane/status,
   - policy artifacts active during the export snapshot.
2) Prefer Edition-based export (signed manifest) to stabilize audits.
3) Define a secession procedure:
   - how a fork is announced,
   - how the final export snapshot is produced,
   - how disputes about export completeness are handled.
4) Preserve “no clean history” principle:
   - suppression over deletion,
   - rationale trails for governance outcomes.
5) Publish a public “fork path” in the nutrition label.

### 9.3 Recommended receipt/outcome types
- `ExportSnapshotReceipt`
- `SecessionNoticeReceipt`
- `ExportDisputeOutcome`

### 9.4 Suggested machine-readable requirements block
```json
{
  "requirements": {
    "export": {
      "export_manifest_required": true,
      "include_receipts_outcomes": true
    },
    "secession": { "procedure_required": true }
  }
}
```

---

## 10. How these charters integrate with WKGC v0.1

WKGC v0.1 defines baseline obligations for knowledge commons contexts (lanes, receipts, service, safety, membranes).

This charter library provides **specialized overlays**. A context may:
- adopt WKGC alone (general commons), or
- adopt WKGC + one or more charters above (domain-specific commons).

Recommended:
- public/general commons: WKGC + IX-1
- media commons: WKGC + EV-1 + IX-1
- software commons: WKGC + IX-1 + ID-1 (scanners)
- directory commons: WKGC + PI-1 + ID-1
- medicine/legal/finance: WKGC + HI-1 + EV-1 + IX-1 + ID-1

---

## 11. Conformance

A context claiming conformance to a charter in this addendum MUST:
- adopt the charter via CharterAdoptionReceipt,
- adopt required policies listed by the charter (or provide superseding equivalents),
- and satisfy the charter’s normative requirements.

NEXUS implementations SHOULD provide a derived conformance report, but conformance is ultimately a governance claim that can be contested.

---
