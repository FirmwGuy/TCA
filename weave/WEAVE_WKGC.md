# WEAVE Addendum — Knowledge Commons Governance for PRISM + NEXUS (WKGC)
SPDX-FileCopyrightText: © 2025–present Victor M. Barrientos <firmw.guy@gmail.com>
SPDX-License-Identifier: CC-BY-SA-4.0
**Version:** 0.1 — replaces prior WKGC drafts; no legacy compatibility assumed  
**Status:** Implementation-oriented addendum (binding when adopted by a Node or Context)  
**Applies to:** WEAVE Nodes operating a communitarian claim-graph knowledgebase  
**Companion specs:** PRISM v1.0, NEXUS v1.0, NEXUS-POLICIES v0.1  
**Last updated:** 2025-12-26

---

## 0. Purpose

This addendum defines how a WEAVE-governed community operates a **Knowledge Commons** whose canonical substrate is:

- PRISM **Claims** and **Evidence** artifacts (content-addressed; optionally signed),
- PRISM **Packs** (hash-pinned meaning definitions),
- WEAVE **Context Declarations** and **Receipts/Outcomes** (governance),
- NEXUS **execution** (storage/query/index/discovery) as a policy-executing runtime.

It is designed to remedy common failure modes of open commons systems:
- vandalism/brigading/harassment,
- doxxing and privacy harm,
- “silent capture” via opaque ranking,
- “clean history” rewriting (deleting uncomfortable past decisions),
- unequal access to sources.

WKGC establishes **governance obligations** and **minimum execution contracts** so:
- open contribution remains possible,
- but **shared memory is gated**, accountable, and forkable.

WKGC does **not** define:
- PRISM artifact schemas (PRISM does),
- the internal database/index implementation (NEXUS does),
- network transport (replication protocols may vary),
- global authority structures.

---

## 1. Stack boundaries and ownership

A WEAVE Node in this system has three distinct authorities:

1) **PRISM (Semantics + Verifiability):**
   - defines meanings and packs,
   - defines Claim/Evidence chain structure,
   - defines PVL (PRISM Verifiability Levels),
   - defines canonical hashing/signing and non-normative discovery overlays.

2) **NEXUS (Execution):**
   - stores and verifies artifacts,
   - validates artifacts against PVL floors,
   - materializes claim graphs,
   - builds indexes and executes discovery pipelines,
   - produces explanations and provenance.

3) **WEAVE (Governance):**
   - defines legitimacy/authority within contexts,
   - defines due process, safety floors, and communal duties,
   - defines receipts/outcomes and which policies are active.

**Invariant (normative):** A UI/search engine MUST NOT become de facto governor.  
Authority exists only via WEAVE-defined contexts, adopted policies, and receipts/outcomes.

---

## 2. Definitions

- **Node:** an operator of a NEXUS instance governed by WEAVE.
- **Context:** a declared governance scope (membership, strictness floor, safety rules, dispute process).
- **Lens:** a user-facing view defined by:
  - (Context(s) + adopted policy bundle + trust anchors + safety settings + index providers + lane selection).
- **Lane:** a lifecycle tier for shared memory within a context.
- **Receipt:** a signed artifact recording a high-impact governance action.
- **Outcome:** a signed artifact recording a governance decision (verdict) that changes visibility/legitimacy states.
- **Proposal:** a candidate artifact set seeking admission to a shared lane/edition.
- **Admission:** inclusion in a shared lane (typically by Edition inclusion and/or AdmissionReceipt).
- **Suppression:** hiding content from default views while preserving audit traces (not deletion).
- **Service:** measurable contribution of resources or labor (storage/relay/verification/review).
- **PVL:** PRISM Verifiability Level (0–3). PVL is verifiability, not truth.
- **M-level:** WEAVE-facing strictness floor label (M0–M3) used for cultural/contract expectations.

---

## 3. What is a “Knowledge Commons Context”?

A **Knowledge Commons Context** is a Context that:
- admits content into shared lanes (admitted/featured),
- provides discovery surfaces for the public or members,
- and therefore MUST follow WKGC conformance rules.

Non-commons contexts (private groups, ephemeral chats, personal notebooks) MAY adopt WKGC partially, but are not required.

---

## 4. Context Declaration (normative)

Every Knowledge Commons Context **MUST** publish a **ContextDeclaration** artifact that can be ingested and executed by NEXUS.

### 4.1 ContextDeclaration artifact type
Recommended:
- `artifact_type = "ContextDeclaration"` (a PRISM artifact type published under WEAVE governance)

### 4.2 Required fields (minimum)
A ContextDeclaration body MUST include:

```json
{
  "context": {
    "context_id": "weave:context:example",
    "context_version": "1",

    "name": { "text": "Example Commons", "lang": "en" },
    "purpose": { "text": "A shared knowledge commons about X.", "lang": "en" },

    "membership": {
      "mode": "public | members_only | restricted",
      "how_to_join": "url-or-instructions",
      "ombuds_contact": "url-or-method"
    },

    "visibility_defaults": {
      "claims": "public | federated | members_only | restricted | local_only",
      "media": "public | federated | members_only | restricted | local_only",
      "software": "members_only | restricted | local_only",
      "pii_directory": "members_only | restricted | local_only"
    },

    "strictness": {
      "m_level": "M0 | M1 | M2 | M3",
      "pvl_floor_default": 2,
      "m_to_pvl_mapping": {
        "M0": 0,
        "M1": 1,
        "M2": 2,
        "M3": 3
      }
    },

    "policies": {
      "safety_policy": "sha256:...",
      "ranking_policy": "sha256:...",
      "quality_policy": "sha256:...",
      "admission_policy": "sha256:...",
      "service_policy": "sha256:...",
      "index_policy": "sha256:...",
      "projection_policy": "sha256:...",
      "ingestion_policy": "sha256:..."
    },

    "charters": [
      { "charter_hash": "sha256:...", "required": true }
    ],

    "membrane": {
      "pack_allowlist": [ "sha256:..." ],
      "template_allowlist_pvl3": [ { "pack_hash": "sha256:...", "entry_uid": "uid:tpl:..." } ],
      "quarantine_lane_enabled": true
    },

    "lanes": {
      "draft": { "indexed_by_default": false },
      "proposed": { "indexed_by_default": false, "opt_in": true },
      "admitted": { "indexed_by_default": true },
      "featured": { "indexed_by_default": true },
      "suppressed": { "indexed_by_default": false },
      "quarantine": { "indexed_by_default": false, "opt_in": true }
    },

    "disputes_and_appeals": {
      "how_to_challenge": "url-or-instructions",
      "timelines": { "response_days": 14, "appeal_days": 30 },
      "anti_harassment_controls": { "rate_limits": true, "budgeted_actions": true }
    },

    "replay": {
      "replay_window_seconds": 2592000
    },

    "retention": {
      "policy_summary": { "text": "What is retained, summarized, pruned, and for how long.", "lang": "en" }
    },

    "export_and_fork": {
      "export_instructions": "url-or-instructions",
      "fork_policy": "url-or-text"
    }
  }
}
```

#### Normative notes
- `strictness.m_level` is a cultural/contract label; PVL is the executable validation floor.
- `policies.*` MUST reference immutable policy artifact hashes executed by NEXUS (see `nexus/NEXUS-POLICIES.md`).
- `charters[]` establishes the set of charters this context intends to run; charter activation still requires adoption receipts (see WCL).
- `replay.replay_window_seconds` MUST be declared so audits can know what “reconstructible” means for this context.
- `retention.policy_summary` MUST be explicit; pruning/summarization actions MUST be logged as events/receipts so “no clean history” remains enforceable.
- `membrane.pack_allowlist` and `template_allowlist_pvl3` MUST be enforced on imports (see §10).
- ContextDeclarations MUST be versioned (`context_version`) and immutable once published. New versions are new artifacts.

### 4.3 High-impact action list (required)
Each ContextDeclaration MUST define what actions are **high-impact** (require receipts/outcomes), including at minimum:
- content admission to shared lanes (admitted/featured),
- content suppression/reproval,
- policy adoption/updates,
- moderator/reviewer role assignment,
- safety exceptions (temporary overrides),
- bans/expulsions (where applicable).

This MAY be represented as:
- a list of receipt/outcome types the context recognizes and requires.

---

## 5. Meaning strictness: M-levels and PVL floors (normative)

WKGC defines a WEAVE-facing strictness dial **M0–M3**. PRISM provides protocol-facing **PVL-0..3**.

### 5.1 Default mapping
Unless a context explicitly overrides it, mapping is:

| M-level | Cultural intent | Minimum executable floor |
|---|---|---|
| M0 | free talk / exploratory | PVL-0 |
| M1 | annotated working knowledge | PVL-1 |
| M2 | grounded interoperable knowledge | PVL-2 |
| M3 | template-validated commitments (high impact) | PVL-3 |

### 5.2 Enforcement expectations
- In **M2 contexts**, any artifact admitted to shared memory lanes MUST satisfy PVL-2 or higher.
- In **M3 contexts**, any high-impact speech act (see §6) MUST satisfy PVL-3, including template validation where required by policy.
- Contexts MAY allow PVL below the floor only in explicitly marked lanes such as `draft`, `proposed`, or `quarantine`.

---

## 6. Receipts and outcomes (normative minimum interface)

NEXUS must compute lane/status from receipts/outcomes. WKGC therefore defines a minimum receipt/outcome set and schemas.

### 6.1 General receipt envelope expectations
Receipts and outcomes:
- MUST be PRISM artifacts (hashed and optionally signed; signatures RECOMMENDED and often REQUIRED by policy),
- MUST reference a `context_id`,
- MUST reference target artifact hashes,
- MUST be immutable once published.

### 6.2 Common receipt fields (required)
All receipt-like bodies MUST include:

```json
{
  "receipt": {
    "context_id": "weave:context:...",
    "receipt_type": "ProposalReceipt | EndorsementReceipt | AdmissionReceipt | PolicyAdoptionReceipt | ...",
    "targets": [ "sha256:..." ],
    "issued_at": "2025-12-26T00:00:00Z",
    "issuer": "did:key:...",
    "evidence": [ { "artifact_hash": "sha256:..." } ],
    "notes": { "text": "Human-readable rationale.", "lang": "en" }
  }
}
```

Rules:
- `targets` MUST be content hashes of PRISM artifacts.
- `evidence` entries MUST be artifact hashes (e.g., Claim evidence chains, Derivations, review transcripts).
- `issuer` identifies the signer/role holder; role validity is governance-specific.

### 6.3 Required receipt/outcome types (minimum)

#### 6.3.1 ProposalReceipt (required for proposed lane)
Represents entry into the `proposed` (or `quarantine`) lane.

Additional fields:
- `requested_lane`: `admitted | featured | other`
- `content_class`: `claims | media | software | pii_directory | dataset | other`

#### 6.3.2 EndorsementReceipt (required for admission workflows)
Represents an approval or endorsement from an eligible reviewer.

Additional fields:
- `vote`: `approve | request_changes | reject | flag_safety`
- `review_scope`: optional list of target hashes if endorsing a subset

#### 6.3.3 AdmissionReceipt (required for admitted lane unless Edition inclusion is used)
Represents a governance act admitting targets into the `admitted` lane.

Additional fields:
- `lane`: `admitted`
- `policy_hashes`: active policy hashes used for the decision
- `basis`: `receipt | edition`

#### 6.3.4 FeatureReceipt (optional but recommended)
Represents promoting targets into `featured`.

Additional fields:
- `lane`: `featured`
- `requirements_met`: optional checklist outcomes

#### 6.3.5 PolicyAdoptionReceipt (required)
Activates a policy artifact within a context.

Additional fields:
- `policy_kind`: `SafetyPolicy | RankingPolicy | QualityPolicy | AdmissionPolicy | ServicePolicy | IndexPolicy`
- `policy_hash`: the adopted policy artifact hash
- `supersedes`: optional previous policy hash

#### 6.3.6 SuppressionOutcome (required pattern)
Represents suppression (hide-from-default) rather than deletion.

Additional fields:
- `lane`: `suppressed`
- `reason_code`: e.g., `PII`, `HARASSMENT`, `MALWARE`, `COPYRIGHT`, `FRAUD`, `OTHER`
- `scope`: `default_views_only | full_block` (full_block still preserves minimal audit traces)

#### 6.3.7 OutcomeReceipt (verdict / reproval / dispute outcome)
Represents a dispute resolution outcome that may:
- retract, supersede, or mark a claim contested,
- impose lane changes (including suppression),
- record sanctions (governance-specific).

Additional fields:
- `outcome_type`: `attest | contest | retract | supersede | sanction | restore | other`
- `references`: artifact hashes for prior outcomes or contested items

#### 6.3.8 ConsentReceipt (required for PII contexts)
Records consent for storing or sharing PII.

Additional fields:
- `subject`: an identifier for the data subject (governance-specific)
- `consent_scope`: allowed visibility/export rules
- `revocation`: optional reference to revocation receipt

#### 6.3.9 ScanReceipt / SandboxReceipt (recommended for software)
Records results of security scans/sandboxing.

Additional fields:
- `tool_identity`: code hash / scanner identity
- `result`: `pass | fail | inconclusive`
- `report_ref`: artifact hash for full report

### 6.4 How NEXUS uses receipts (normative behavior)
A NEXUS implementation operating a WKGC context MUST:
- compute lane membership from receipts/outcomes and/or adopted Editions,
- expose which receipt hashes caused a status,
- never allow a mutable “lane flag” to override receipts/outcomes.

---

## 7. Lanes and lifecycle: Proposal → Review → Admission (normative)

Open contribution is allowed, but **shared memory is gated**.

### 7.1 Required lanes (minimum)
Each Knowledge Commons Context MUST support at least:

1) **draft (local):** not shared by default; not indexed globally  
2) **proposed:** visible in opt-in proposal indexes; not in default discovery  
3) **admitted:** included in shared editions or admitted by receipts; eligible for default discovery  
4) **featured (optional but recommended):** higher bar; curated surfaces  
5) **suppressed:** hidden by default (not deletion)

Recommended additionally:
- **quarantine:** for imports from unknown sources; opt-in exploration.

### 7.2 Admission is receipt-driven
Admission MUST be represented as one of:
- inclusion in a signed **Edition** adopted by this context, OR
- an explicit `AdmissionReceipt` referencing the targets.

### 7.3 Proposal mechanics (recommended minimum)
A proposal SHOULD include:
- artifact hashes (targets),
- content class,
- license/provenance metadata (where applicable),
- evidence expectations (for claims),
- safety self-labeling (risk categories),
- requested lane.

### 7.4 Randomized review assignment (recommended)
Contexts SHOULD use randomized review queues to reduce collusion and distribute labor.

Assignment SHOULD respect:
- eligibility (service credits + roles),
- workload caps,
- diversity constraints (avoid clique capture).

### 7.5 Content class differentiation (required concept)
Admission rules MUST differentiate at least:
- **claims:** evidence and dispute handling
- **media:** provenance/licensing and safe preview gating
- **software:** sandbox/scanning and “never auto-run”
- **PII directories:** consent receipts, encryption, restricted visibility

---

## 8. Service and anti-cheap-influence (normative concept)

This system does not assume altruism. It requires **measurable stake**.

### 8.1 ServicePolicy is required for admitting contexts
Any context that admits shared content MUST adopt a ServicePolicy describing:
- service credits unit and decay,
- tier thresholds,
- budgets (endorsements/challenges/proposals),
- probation rules for newcomers,
- non-transferability.

### 8.2 Budgeted influence actions (required concept)
High-impact actions that change shared memory MUST be budgeted and attributable, including:
- endorsements,
- challenges/contests,
- admissions/features,
- suppressions.

NEXUS MUST enforce budgets as declared by policy and surface budget state in explanations where relevant.

---

## 9. Discovery governance obligations (required)

### 9.1 Separation: safety gating vs quality ranking
Contexts MUST adopt policies that distinguish:
- **SafetyPolicy:** hard constraints (what can be shown/previewed)
- **RankingPolicy / QualityPolicy:** ordering and minimum bars within the allowed set

Safety MUST apply before ranking.

### 9.2 “Can’t be unseen” protections
SafetyPolicy MUST define:
- preview rules (blur/no autoplay/metadata-only),
- risk categories and thresholds,
- PII constraints for directories,
- exposure limits (optional).

### 9.3 Explanation requirements
RankingPolicy MUST require that NEXUS can show:
- index/provider identity,
- policy hashes used,
- top “why” factors for rank,
- safety bucket classification (ALLOW/WARN/BLUR/METADATA/BLOCK),
- declared PVL vs validated PVL (where computed).

### 9.4 Replayability expectations (recommended)
IndexPolicy SHOULD require IndexSnapshots and replay recipes at least for:
- default public indices,
- high-impact contexts (science/legal/health),
within a declared replay window.

### 9.5 Contesting rankings and labels
Contexts MUST provide challenge pathways for:
- risk labels,
- ranking policy misuse,
- index provider misconduct,
- biased/unsafe discovery outputs.

Outcomes SHOULD be receipts/outcomes and MUST be forkable.

---

## 10. Federation membranes and quarantine (required concept)

### 10.1 Edition-first federation (recommended)
Cross-node exchange SHOULD use signed Editions because it:
- stabilizes auditing,
- simplifies provenance,
- reduces “stream poisoning.”

### 10.2 Membrane rules (required)
Receiving contexts MUST apply membrane rules to imported artifacts/editions:
- pack allowlists (meaning supply chain constraints),
- template allowlists for PVL-3/high-impact artifacts,
- safety policy compatibility checks,
- quarantine for unknown or low-trust sources.

### 10.3 Quarantine lane (recommended)
Contexts SHOULD provide a quarantine lane where:
- imported content can be explored by opt-in users,
- default discovery excludes it,
- safety gates remain enforced.

---

## 11. Privacy, directories, and “right to remove” (required)

Append-only systems cannot truly erase history everywhere; governance must mitigate harm.

### 11.1 Consent-first for PII (required)
Contexts that store PII MUST:
- define consent requirements,
- define how consent is recorded (ConsentReceipt recommended),
- define who may view/export records,
- provide a “subject access” pathway.

### 11.2 Suppression over deletion (required pattern)
“Removal” is implemented by:
- `SuppressionOutcome` (hide from default views),
- stopping replication (where applicable),
- key rotation/re-encryption (for members-only data),
- retaining minimal audit traces with graduated disclosure.

Minimum audit trace requirement (required):
- Any suppression/redaction/erasure-relevant action MUST leave an immutable governance artifact (receipt/outcome) that:
  - references the affected artifact hash(es),
  - includes `context_id`, `issued_at`, and an attributable `issuer` (or privacy-safe role identifier),
  - includes a public-facing rationale summary (which MAY be redacted),
  - optionally links to an encrypted/restricted “deep record” for legitimate audits.
- Redaction MAY hide sensitive details, but the existence of the action and its linkage to the affected artifact(s) MUST remain referenceable (“no clean history”).

### 11.3 PII search constraints (required)
Directory contexts MUST ensure:
- PII does not enter public/federated indexes,
- results default to metadata-only where appropriate,
- membership checks are applied before revealing payload.

---

## 12. Minimum public transparency surface (“nutrition label”) (required)

Each Knowledge Commons Context MUST expose a public-facing transparency surface sufficient for outsiders to understand:
- what they are looking at,
- what rules govern it,
- and how to exit/fork.

### 12.1 Required nutrition label fields
The label MUST include:
- context name and purpose,
- membership mode and contact/ombuds,
- meaning strictness level (M0–M3) and PVL floor mapping,
- safety floor summary (preview rules, blocked categories),
- admission policy summary (who can admit, how),
- service policy summary (how influence is earned, decay, budgets),
- index/ranking policy identifiers (hashes) and provider list,
- dispute and appeal links,
- export/fork path.

Deep records may be restricted/encrypted, but the label must be public enough to be meaningful.

### 12.2 Recommended machine-readable label artifact
Recommended:
- `artifact_type = "NutritionLabel"`
- generated as a derived artifact from ContextDeclaration + policy hashes (and MAY be persisted as a stable PRISM artifact for convenience, but remains derived/non-authoritative).

---

## 13. Conformance checklists

### 13.1 Context conformance (WKGC v0.1)
A Knowledge Commons Context is conformant if it:
1) Publishes a ContextDeclaration with required fields (including policy pointers, `charters[]`, and replay/retention declarations) (§4).  
2) Uses receipts/outcomes for admission, suppression, policy adoption, and dispute outcomes (§6–§7).  
3) Implements proposal → review → admission lanes (or equivalent) and does not default-index proposals (§7).  
4) Enforces service/budget rules for high-impact actions (§8).  
5) Enforces safety gating and prevents PII leakage (§9, §11).  
6) Preserves minimum audit traces for suppression/redaction/retention actions and supports audits within the declared replay window (§11, §4).  
7) Ensures discovery is plural, policy-driven, and explainable (§9).  
8) Provides dispute and appeal pathways with anti-harassment controls (§4, §9).  
9) Provides export/fork capability via Editions or equivalent (§10, §12).  

### 13.2 Node conformance (WKGC v0.1)
A Node is conformant if it:
- hosts at least one conformant Knowledge Commons Context,
- supports Edition import/export,
- supports publishing/verifying receipts/outcomes,
- and provides a public nutrition label for each commons context it serves.

---

## Appendix A — Recommended policy artifact types (informative)

Contexts SHOULD adopt policy artifacts executed by NEXUS (schemas in `nexus/NEXUS-POLICIES.md`):
- `SafetyPolicy`
- `QualityPolicy`
- `RankingPolicy`
- `AdmissionPolicy`
- `ServicePolicy`
- `IndexPolicy`
- `ProjectionPolicy`
- `IngestionPolicy`

---

## Appendix B — Recommended receipt/outcome types (informative)

- `ProposalReceipt`
- `EndorsementReceipt`
- `AdmissionReceipt`
- `FeatureReceipt`
- `PolicyAdoptionReceipt`
- `SuppressionOutcome`
- `OutcomeReceipt` (verdict/reproval)
- `ConsentReceipt` (PII contexts)
- `ScanReceipt` / `SandboxReceipt` (software lane)

---

## Appendix C — Minimal lane-to-status computation (informative)

A simple derived status algorithm for a context:
1) If any `SuppressionOutcome` applies and is active → lane = suppressed  
2) Else if any `FeatureReceipt` applies (and prerequisites satisfied) → lane = featured  
3) Else if admitted by Edition inclusion or `AdmissionReceipt` → lane = admitted  
4) Else if proposed by `ProposalReceipt` → lane = proposed  
5) Else → lane = draft/local (implementation-defined)

This is illustrative; contexts MAY define richer rules by policy, but MUST remain receipt/outcome-driven.

---
