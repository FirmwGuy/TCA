# The Trust Commons Accord

**TCA v0.1 — Draft**
*A constitution for shared meaning, accountable intelligence, and earned trust.*

---

## Preamble

Trust is the scarce resource of the digital age.

Storage is abundant. Compute is abundant. Speech is abundant. Even “intelligence” is becoming abundant. What remains scarce is the ability to **rely**—to act together without being silently coerced, misled, captured, or drowned in noise.

The Trust Commons Accord (TCA) is a commitment to build systems where:

* meaning is explicit and verifiable,
* legitimacy is context-scoped and auditable,
* execution is transparent and replayable,
* intelligence is assistive—not sovereign,
* and communities retain credible exit through forkability.

This Accord exists so people can build **Commons**—for knowledge, creativity, markets, coordination, and collective learning—without sacrificing safety, privacy, pluralism, or dignity.

---

## 1. Status and scope

### 1.1 What TCA is

TCA is the **constitutional layer** of a stack that includes:

* **PRISM** (verifiable semantics and claims),
* **Governance Profiles** (default: **WEAVE**),
* **NEXUS** (policy-executing storage/query/discovery/projection runtime),
* **CEP Substrate** (deterministic runtime and enforcement substrate),
* optional agent/learning layers (e.g., **PRAXIS**).

PRISM explicitly does **not** define governance or execution; those belong to governance and runtime layers. TCA defines the **non-negotiable invariants** that these layers must respect.

### 1.2 What TCA is not

TCA is not:

* a single global authority,
* a single moderation policy,
* a single ranking or recommendation regime,
* a single transport or federation protocol.

(These “not-a-single-global-authority / not-a-ranking-system” boundaries are consistent with WEAVE’s framing as governance infrastructure rather than centralized control.)

---

## 2. Conformance language

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** are normative.

(Implementations commonly adopt RFC 2119 semantics; NEXUS and PRISM already do.)

---

## 3. Constitutional layering and separation of powers

### 3.1 The trust stack

TCA recognizes five distinct powers—kept separate to prevent capture:

1. **Meaning power** (semantics): *What does a claim mean?*
   → Provided by PRISM meaning packs and meaning references.

2. **Legitimacy power** (governance): *Why does this claim count here?*
   → Provided by governance profiles (default: WEAVE), via context declarations, receipts, and outcomes.

3. **Execution power** (runtime): *What does the system do, deterministically, with these artifacts and policies?*
   → Provided by NEXUS and its runtime substrate (e.g., CEP).

4. **Interface power** (UI/search/recommendation): *What is shown first? How is it navigated?*
   → Always **derived** and never allowed to become sovereign.

5. **Intelligence power** (analysis/agents/learning): *What suggestions are generated? What patterns are proposed?*
   → Always **assistive** and never allowed to silently author canonical reality.

### 3.2 The anti-capture invariant

A UI/search engine MUST NOT become the de facto governor. Authority exists only via context-scoped governance, adopted policies, and governance artifacts (receipts/outcomes).

### 3.3 Governance profile status

Governance is provided by **profiles**, not by hardcoded global rule.

WEAVE is RECOMMENDED as the default governance profile because it is explicitly built for:

* core rights and safeguards,
* due process,
* evidence-first decision trails (“receipts”),
* anti-capture properties (earned influence, forkability),
* modular rule-sets (charters) per “room”.

However, WEAVE is not “the constitution.” Under TCA, WEAVE is a **governance profile** operating under constitutional constraints.

---

## 4. Trust primitives

TCA recognizes the following primitives as constitutional (even if names vary by implementation):

### 4.1 Contexts and lenses

* **Context**: a declared scope in which legitimacy is defined and enforced.
* **Lens**: a user-facing view configuration over contexts and policies.

(These concepts are central to WEAVE/WKGC definitions and required to prevent “global discovery” from becoming “global control”.)

**Invariant:** Legitimacy is context-scoped. No claim has universal authority by default.

### 4.2 Claims, evidence, and verifiability

Claims SHOULD be evidence-anchored. Evidence identity SHOULD be durable and content-addressed.

NEXUS formalizes “evidence-first” and content-addressing as a design principle.

**Invariant:** PVL (PRISM Verifiability Level) is verifiability—not truth and PRISM explicitly rejects “deciding truth” as a non-goal.

### 4.3 Receipts, outcomes, and due process

High-impact actions (admissions, suppressions, role changes, sensitive access grants, etc.) MUST produce governance artifacts that are:

* append-only,
* auditable,
* attributable.

WEAVE’s model of receipts/outcomes is explicitly intended as evidence-first legitimacy.

---

## 5. Canonical reality vs derived products

### 5.1 The hard boundary

Systems adopting TCA MUST maintain a **hard boundary** between:

* **Canonical artifacts**: the durable meaning-bearing inputs (claims, packs, evidence, governance artifacts, editions/manifests, etc.)
  and
* **Derived products**: indexes, embeddings, rankings, previews, projections/pages, caches, candidate extraction outputs, and other convenience layers.

Derived products MUST be discardable without loss of canonical meaning and provenance-tagged to canonical inputs.

Derived products MAY be **persisted** as artifacts for audit/replay convenience (e.g., snapshots or candidate bundles), but persistence does not grant authority: they remain derived, provenance-tagged, and reconstructible within declared replay windows.

### 5.2 Safety before relevance

Discovery and projection MUST enforce:

1. safety gating, then
2. PVL/validity gating, then
3. ranking/ordering.

This is a constitutional trust rule: **relevance must never override safety or legitimacy**.

### 5.3 No hidden authority

No single index, renderer, or analyzer is allowed to become “the truth” by default.

---

## 6. Accountability: authorship, agency, and signatures

### 6.1 The authorship invariant

In the **Human Commons**, **no AI agent may write canonical claims without an accountable human principal authorizing the write**.

Constitutional intent:

* Trust cannot be created anonymously.
* Costs of publication cannot be externalized.
* Governance must have a responsible party for remediation.

### 6.2 What counts as “writing”

“Writing to the Commons” includes:

* submitting a claim artifact into any lane that is visible beyond the requesting human’s private workspace,
* publishing a pack release,
* issuing canonical governance artifacts (receipts/outcomes),
* publishing editions/manifests.

Drafts, analyses, candidate bundles, and ephemeral suggestions that are not published as canonical artifacts MAY be produced without signature—provided they remain non-canonical and non-authoritative.

(Consistent with NEXUS treating CandidateBundles as derived products; they may be persisted as non-claim artifacts for audit, but they remain derived and non-authoritative.)

### 6.3 Personas and delegation

A “human principal” may operate through personas or roles (e.g., Constella personas), but responsibility must remain attributable to an accountable human or accountable organization.

---

## 7. Privacy, delicacy, and the “can’t-unsee” constraint

### 7.1 Open by default, protected where required

TCA’s default posture is **open participation**—but with explicit protection for content that is:

* private,
* sensitive,
* secret,
* delicate,
* or subject to “can’t-unsee” constraints.

NEXUS explicitly requires protections to prevent sensitive payloads from leaking into public or federated derived indexes and to apply safety gating to previews/excerpts.

### 7.2 Sealed Access (SAD)

For sensitive/delicate/can’t-unsee content, AI access MUST be mediated by a **Sealed Access Dossier** (SAD) or equivalent governed authorization that binds:

* scope (what can be accessed),
* purpose (why),
* duration,
* obligations (non-retention, non-federation, redaction),
* auditability.

This is constitutional because “can’t-unsee” failure modes are irreversible; access must therefore be explicit, scoped, and accountable.

#### 7.2.1 Recommended SAD interface (minimum)

Implementations MAY represent a SAD as a PRISM extension artifact type (recommended):
- `artifact_type = "SealedAccessDossier"`

Minimum body (example):

```json
{
  "sad": {
    "context_id": "weave:context:...",

    "subject": { "kind": "agent | human | role", "id": "did:key:..." },
    "purpose": { "text": "Why access is requested.", "lang": "en" },

    "scope": {
      "artifacts": ["sha256:..."],
      "content_classes": ["claims","media","software","pii_directory","dataset","other"]
    },

    "duration": { "not_before": "2025-12-26T00:00:00Z", "not_after": "2025-12-27T00:00:00Z" },

    "obligations": {
      "non_retention": true,
      "non_federation": true,
      "redaction_required": true,
      "no_training": true
    },

    "audit": {
      "log_access": true,
      "access_log_visibility": "public_summary | restricted",
      "replay_window_seconds": 2592000
    }
  }
}
```

Governance profiles (e.g., WEAVE/WKGC) define who may issue SADs and how disputes/appeals apply.

### 7.3 Selective disclosure

Systems SHOULD support “public summary + private deep record” transparency patterns when needed for trust without forced disclosure.

DIYA explicitly requires a two-layer transparency model (public “nutrition label” + encrypted “deep record”) and commit–reveal for prompts to preserve auditability without forcing public prompt exposure.

### 7.4 Key-based erasure and controlled decryption

Runtime substrates SHOULD support:

* encryption/signing,
* redaction and key-based erasure,
* controlled decryption at membrane ports.

CEP’s implementation references privacy hooks including payload cryptography and key-based erasure semantics.

---

## 8. Determinism, replay, and auditability

### 8.1 Deterministic execution is constitutional

Where the system’s outputs affect legitimacy, visibility, safety, or durable state, execution MUST be:

* deterministic (or decision-recorded),
* replayable for audit,
* attributable to declared methods and policies.

CEP’s kernel contract is explicitly built around deterministic **Capture → Compute → Commit** phases, with nondeterminism recorded via Decision Cells for replay.

### 8.2 Replay as a civic right

Participants and auditors SHOULD be able to reconstruct:

* what rules were active,
* what artifacts were canonical,
* what derived processes produced an output,
* and why an item was shown/hidden/gated.

NEXUS formalizes runtime neutrality (“surface why shown/hidden/gated”) and derived provenance expectations.

---

## 9. Pluralism, forkability, and credible exit

### 9.1 Pluralism without fragmentation

Multiple contexts, lenses, indexes, analyzers, and renderers MAY coexist so long as:

* their authority is explicit and scoped,
* canonical meaning and provenance remain consistent,
* derived influence does not become hidden governance.

NEXUS explicitly supports pluralism (multiple contexts/lenses/providers) and forbids any single index/renderer/analyzer from becoming truth by default.

### 9.2 Forkability is a constitutional safeguard

If governance is captured, participants MUST retain a credible path to exit:

* export canonical artifacts,
* reproduce history and legitimacy evidence,
* continue under a new governance regime.

Constella calls forkability out as a core principle: signed/content-addressed data enables communities to fork if governance is captured.

---

## 10. Earned trust: rights and duties

Trust cannot be granted by loudness, volume, or automation.

Constella’s design goal is explicit: “Speech is cheap, influence is earned” and “rights come with duties”.

### 10.1 Rights (minimum)

In any TCA-aligned Commons, participants MUST have:

* **Right to legible rules** (declarations and policies must be visible)
* **Right to contest and appeal** (due process pathways exist and are auditable)
* **Right to doubt (without harassment)**—structured challenges, rate limits, penalties for abuse (Constella)
* **Right to choose lenses** (global discovery must not force a single regime)
* **Right to credible exit** (forkability)

### 10.2 Duties (per-context, but constitutionally permitted)

Contexts MAY require duties proportional to the influence they grant, including:

* review work,
* storage/relay contributions,
* compute contributions,
* bonds/stakes,
* calibrated civic tasks.

(Constella gives a concrete duty model (storage/review/compute/bond/vouching), but TCA leaves exact economics to charters and profiles.)

---

## 11. The Two Commons: Human Commons and Machine Pattern Commons

TCA adopts a **two-commons architecture** as a constitutional pattern:

### 11.1 Human Commons (HC)

The Human Commons exists to store and govern:

* human-facing knowledge claims,
* creative works and provenance,
* civic coordination artifacts.

DIYA’s values—intent-first, seed-first, process-respect, verifiable provenance, privacy by design—are exemplary for HC creative contexts.

**HC invariant:** AI-authored canonical writes require a human principal signature (Section 6).

### 11.2 Machine Pattern Commons (MPC)

The Machine Pattern Commons exists to store and govern:

* patterns, methods, configurations, evaluations,
* learnings intended to propagate to other CEP instances,
* machine-operational claims that improve service to humans.

**MPC invariant:** MPC governance MUST prevent silent contamination of HC and MUST preserve provenance of methods and derivations.

### 11.3 Interoperation rules

* CEP minds MAY read from HC (subject to sensitivity/SAD rules).
* Writes to HC remain signature-gated (Section 6).
* Patterns derived from HC MAY be published to MPC only under explicit policy, provenance, and (where required) redaction/aggregation constraints.

---

## 12. Intelligence is optional; sovereignty is not

### 12.1 No critical trust path depends on AI

Canonical storage, legitimacy, safety enforcement, and replay MUST NOT depend on model quality.

NEXUS explicitly states AI is not required: deterministic rendering (PSR) must exist, and AI layers must fall back if constraints fail.

### 12.2 AI as “assist,” not authority

AI systems may:

* propose candidate claims,
* summarize,
* extract,
* suggest mappings,
* critique and draft.

But AI outputs are **derived** unless and until:

* they are published as canonical artifacts under policy,
* and (for HC) signed by a human principal.

---

## 13. Adoption and compliance

### 13.1 Adoption statement

A node/community adopting TCA SHALL:

* publish a declaration identifying the TCA version adopted,
* identify the governance profile(s) in force (WEAVE recommended),
* identify the semantics protocol (PRISM),
* identify execution engine(s) (NEXUS or equivalent),
* identify runtime substrate guarantees (e.g., CEP determinism/replay).

### 13.2 Conformance tests

Implementations SHOULD publish conformance evidence that:

* the canonical/derived boundary is enforced,
* safety/PVL gating precedes ranking,
* UI/search cannot become governor,
* sensitive content does not leak into derived indexes,
* Human Commons authorship constraints are enforced (AI-assisted canonical writes require an accountable human principal authorization),
* deterministic/replay properties are maintained for governance-relevant operations.

---

## 14. Amendment and versioning

### 14.1 Amendments must be legible and replayable

Any amendment to TCA MUST:

* be proposed as a versioned artifact,
* include rationale, dissent, and intended effects,
* be adopted through auditable governance outcomes.

WEAVE’s norm of versioned declarations with recorded change rationale is a model for this kind of legibility.

### 14.2 Backward reference

Older versions MUST remain referenceable (with redactions where legally or safety necessary), so that legitimacy history cannot be erased by rewriting rules.

---

# Appendix A. Guiding inspirations (non-normative)

### A.1 Creative trust (DIYA)

DIYA frames trust in creative work as:

* intent-first,
* seed-first,
* process-respect,
* verifiable provenance,
* privacy by design,
  with selective disclosure patterns like public labels + encrypted deep records and commit–reveal prompts.

### A.2 Social trust (Constella)

Constella frames trust as:

* people as anchors, claims as durable atoms,
* scoped trust converting participation into influence,
* “speech is cheap, influence is earned,”
* right to doubt via structured challenges.

These are not just product ideas—they are constitutional intuitions about trust.

---

# Appendix B. Glossary (minimal)

* **Canonical artifact**: durable meaning-bearing input (claims, packs, evidence, governance artifacts).
* **Derived product**: index/ranking/page/embedding/candidate output; discardable and reconstructible.
* **Persisted-derived artifact**: a derived product stored as an artifact for audit/convenience; still derived and non-authoritative.
* **Context**: governance scope defining legitimacy.
* **Lens**: a view over contexts and policies; prevents global control.
* **HC**: Human Commons.
* **MPC**: Machine Pattern Commons.
* **SAD**: Sealed Access Dossier (governed access contract for sensitive/can’t-unsee content).
* **PVL**: verifiability level; not truth.
* **Receipt/Outcome**: governance artifacts establishing accountability.

---

## Closing

TCA is an invitation and a constraint.

An invitation to build together—knowledge commons, creative commons, coordination commons—that can survive the AI era without collapsing into propaganda, spam, coercion, or opaque technocracy.

A constraint that says: **trust must be earned, rules must be legible, and power must remain separable and contestable.**
