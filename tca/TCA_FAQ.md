# Trust Commons Accord (TCA) — FAQ
**Version:** v0.1 — Draft  
**Last updated:** 2025-12-26  

This FAQ explains the Trust Commons Accord (TCA) and how it relates to PRISM, WEAVE, NEXUS, CEP Substrate, and **PRAXIS** (the non‑sovereign intelligence layer).

---

## 1) What is the Trust Commons Accord (TCA)?
TCA is the **constitutional layer**: a small set of invariants and “rules of the game” for building *trustworthy commons* in the age of AI.

It defines:
- what kinds of power must remain separated (meaning vs governance vs execution vs interface vs intelligence),
- what must be attributable,
- what must remain auditable and forkable,
- and what must never become “the governor by accident” (e.g., UI ranking).

TCA is intentionally **not** a governance mechanism by itself.

---

## 2) Is TCA “a platform” or “a protocol”?
TCA is neither a platform nor a transport protocol.

It is a **constitution** that multiple implementations can conform to.  
Protocols and engines sit *under* it (PRISM, NEXUS) and profiles sit *under* it (WEAVE).

---

## 3) How does PRISM relate to TCA?
PRISM is the **meaning/verifiability layer**. It defines canonical, content-addressed artifacts for meanings, claims, evidence, and PVL verification rules, while explicitly *not* defining governance, execution, or truth.  

See PRISM’s scope and non-goal statements.  

---

## 4) Does PRISM decide “truth”?
No. PRISM is explicit that deciding truth is a **non-goal**: it classifies *verifiability/grounding*, not truth.  

---

## 5) How does WEAVE relate to TCA?
WEAVE is the **default governance profile** under TCA.

- TCA says what legitimacy *must* look like (auditable, attributable, contestable, forkable; UI must not become sovereign).
- WEAVE says how to implement that legitimacy in practice: contexts, receipts, outcomes, lanes, dispute paths, duties, etc.

WEAVE is strong and opinionated—but not a constitution.

---

## 6) How does NEXUS relate to TCA?
NEXUS is the **execution engine** for a governance-aware PRISM claim graph. It stores canonical artifacts, validates PVL, executes governance lane/status rules from receipts/outcomes, and produces derived discovery/projection experiences.  

NEXUS explicitly does **not** define meaning, legitimacy, or truth—it executes declared policy.  

---

## 7) What is “canonical vs derived” and why is it central?
NEXUS draws a **hard boundary**:

- **Canonical inputs**: PRISM artifacts + governance artifacts (receipts/outcomes) + editions/manifests.
- **Derived products**: indexes, rankings, pages, previews, embeddings, candidate extraction outputs (unless stored canonically), caches.

Derived products must be discardable and provenance-tagged, so “search didn’t become law” and “summaries didn’t become facts.”

---

## 8) What is PSR, and why does it matter?
PSR (PRISM Surface Realizer) is the **deterministic, non‑AI** renderer that turns a *policy-selected* set of claims into readable pages.  

It is designed to be a “language filter,” not a thinker: it does not decide importance, cannot introduce new facts, and does not fetch external data.  

This makes PSR a safe baseline and audit fallback.

---

## 9) Is AI required for TCA/NEXUS/WEAVE systems?
No.

NEXUS explicitly requires deterministic rendering via PSR and treats AI rendering as optional/derived; if AI fails constraints, it must fall back to PSR.

In other words: **trust must not depend on model quality**.

---

## 10) What is CEP Substrate in this picture?
CEP Substrate is the deterministic runtime substrate that can host NEXUS and other upper layers.

The implemented kernel contract includes deterministic **Capture → Compute → Commit** heartbeats and records nondeterminism via Decision Cells/ledgers for replay.

This is how “auditability” becomes real, not just aspirational.

---

## 11) What is PRAXIS?
**PRAXIS** is a **non‑sovereign intelligence layer** for analysis, extraction, pattern discovery, and assistance—never legitimacy.

Earlier drafts referred to this as the CEP intelligence layer.

Praxis may generate suggestions (e.g., PSA candidate bundles, drafts), but it cannot *make anything count* by itself.

---

## 12) What is a Context? What is a Lens?
In WEAVE/WKGC terms:
- A **Context** is a declared governance scope.
- A **Lens** is a user-facing view defined by (contexts + policies + trust anchors + safety settings + lane visibility + index providers).

Lenses exist so there is no single global “feed” that becomes the governor.

---

## 13) What are “lanes”?
Lanes are lifecycle tiers for content inside a context (e.g., draft → proposed → admitted → featured → suppressed, with optional quarantine).

Lane membership is computed from receipts/outcomes and/or edition inclusion—not mutable flags.

---

## 14) What is PVL and what does it mean?
PVL (PRISM Verifiability Level) measures what can be mechanically verified about a claim and its dependency closure.

PVL is explicitly **not a truth score**.

---

## 15) Can AI read from the Human Commons?
Yes—by default, reading is allowed subject to:
- PVL floors,
- lane/safety policy,
- and sensitivity controls for private/delicate material.

Reading is not authority.

---

## 16) Can AI write to the Human Commons?
Only with an accountable **human principal** authorizing the write (TCA’s “writer pays the cost” principle).

AI outputs without a human signature can still exist as **ephemeral drafts** or **derived candidates** shown to the requester, but they do not become canonical claims until signed and published under context policy.

---

## 17) Who is responsible for an AI-authored claim in Human Commons?
The human principal who authorized publication is responsible for introducing it into shared space.

This establishes accountability for remediation and dispute, but responsibility can diffuse later as the claim is reviewed/admitted under governance.

---

## 18) What about “delicate”, “secret”, or “can’t-unsee” knowledge?
TCA requires stronger handling for sensitive materials.

The recommended mechanism is a **Sealed Access Dossier (SAD)**: a governed, time-bounded, purpose-bound authorization that enables *scoped computation* without allowing irreversible learning or uncontrolled export.

---

## 19) Does “deterministic execution” mean “never delete anything”?
No.

Determinism protects **replayability of authority-relevant outcomes**, not infinite retention of every operational trace.

GC pruning, summarization, and deletion are acceptable if:
- the retention policy is explicit,
- pruning actions are logged as events,
- and the system preserves enough canonical evidence/explanations to audit outcomes within declared replay windows (declared in Node/Context declarations; e.g., `ContextDeclaration.replay.replay_window_seconds` in WKGC-style contexts).

---

## 20) How do we avoid “clean history” rewriting?
WKGC explicitly treats “clean history” rewriting as a failure mode and requires governance actions to be recorded as receipts/outcomes rather than silently erased.

Suppression/hiding is different from deletion: you can hide content from default views while preserving audit traces.

---

## 21) How does federation work under TCA?
Federation is based on exchanging canonical artifacts and (optionally) signed editions/manifests.

TCA treats “credible exit” as a safeguard: communities must be able to fork and migrate without losing audit trails.

---

## 22) How do DIYA and Constella fit?
They are “platforms on top” that benefit from TCA invariants:

- DIYA emphasizes selective disclosure and signed provenance (“public nutrition label” + encrypted deep record; commit–reveal prompts; signatures for integrity).
- Constella emphasizes anti‑capture: “speech is cheap, influence is earned,” versioned objects, structured dispute/outcomes, and forkability.

Both align naturally with TCA’s trust-first constitution.

---

## 23) What does it mean that WEAVE is “default”?
It means:
- WEAVE is the recommended, reference governance profile that satisfies TCA invariants.
- Other governance profiles can exist, but must still comply with TCA’s constitutional floor.

Default ≠ optional; default means “what you get if you don’t want to design governance from scratch.”

---

## 24) What is required to “adopt TCA” as a node/community?
A TCA‑adopting node should publish:
- which TCA version it adopts,
- which governance profile(s) it runs (WEAVE recommended),
- which semantics layer it uses (PRISM),
- which execution engine it uses (NEXUS or equivalent),
- and conformance evidence (tests/logs) that key invariants hold (canonical vs derived boundary, UI non-sovereignty, auditability, sensitivity controls).

---

## 25) What should I read next?
Suggested order:
1) PRISM (semantics + PVL; what it does *not* do)
2) WEAVE/WKGC (governance invariants: UI must not become governor; contexts/lenses/lanes)
3) NEXUS (canonical vs derived; safety before ranking; runtime neutrality)
4) PSR (deterministic rendering)
5) CEP Implementation Reference (deterministic kernel + replay)
6) DIYA / Constella (example upper-layer products that benefit from TCA)

---

## Glossary (mini)
- **Canonical**: durable, meaning-bearing artifacts (claims, evidence, governance receipts/outcomes).
- **Derived**: discardable projections (indexes, rankings, pages, summaries, embeddings).
- **Context**: governance scope.
- **Lens**: view configuration over contexts/policies/indexes.
- **Lane**: lifecycle tier for content within a context.
- **PVL**: verifiability level (not truth).
- **SAD**: Sealed Access Dossier (scoped authorization for sensitive content).
- **PSR**: deterministic renderer (“language filter”).
- **PSA**: candidate extraction (deterministic or model-assisted), routed to review lanes.

---
