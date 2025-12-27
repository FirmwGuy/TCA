# The Weave Governance Constitution FAQ (Expanded)
**Version:** v0.2 (FAQ)  
**Status:** Draft — explanatory; not normative  
**Updated for:** PRISM v1 + NEXUS v1 + WKGC v0.1 + WEAVE Charter Library Addendum (WCL) v0.1  
**Last updated:** 2025-12-26

> **This FAQ is not law.**  
> The binding texts are the WEAVE Constitution and any Charters/Addenda a Node or Context explicitly adopts.  
> This FAQ exists to make the “governance gears” legible to builders and community stewards.

---

## Table of contents

- A. Orientation (what WEAVE is and is not)
- B. Node vs Context vs Lens (and why declarations matter)
- C. Meaning strictness (M0–M3) and how PRISM fits
- D. Evidence-first governance: receipts, outcomes, and “no clean history”
- E. Charters, policies, and “governance gears”
- F. Knowledge Commons operations (WKGC): lanes, admission, suppression
- G. Trust, influence, and service: budgets and anti-cheap-influence
- H. Safety, privacy, and graduated disclosure
- I. Discovery, indexes, and resisting “search capture”
- J. Forkability, export, and secession
- K. High-asymmetry governments (extra safeguards)
- L. How WEAVE evolves without a head
- M. Commons, markets, arena, and cultural questions
- N. Implementation: what “WEAVE-compliant” should mean in practice
- O. Glossary (quick reference)

---

# A. Orientation

### 1) What is WEAVE, in plain terms?
WEAVE is a governance framework for communities (online or offline) that want:
- strong core rights and safeguards,
- due process for conflicts and harms,
- evidence-first decision trails (“receipts”),
- anti-capture properties (earned influence, forkability),
- and modular domain rule-sets (Charters) so different “rooms” can have different norms without confusion.

### 2) What is WEAVE *not*?
WEAVE is *not*:
- a single global authority,
- a content ranking system,
- a single moderation policy,
- a single technical protocol for storage or transport.

WEAVE is “what must be true” about governance; PRISM+NEXUS are “how it can be executed and audited.”

### 3) Why does WEAVE care about “interface,” not just values?
Because values that cannot be executed and audited collapse under pressure.
WEAVE is designed to force legibility: declarations, receipts, contestation pathways, and credible exit.

### 4) What’s the simplest mental model?
Think of WEAVE as:
- **Constitution:** always-on rights + safeguards
- **Charters:** adoptable “governance plugins” (markets, commons, high-impact domains, etc.)
- **Receipts/Outcomes:** append-only accountability records for high-impact actions
- **Contexts:** scoped “rooms” that declare strictness, policies, and dispute process

---

# B. Node vs Context vs Lens

### 5) What is a Node?
A **Node** is a community that adopts the WEAVE Constitution and publishes a **Node Declaration**: what it has adopted and how governance works at the community level.

### 6) What is a Context?
A **Context** is a specific governed space inside a Node (topic room, project room, market room, gallery lane, commons lane).  
Contexts publish a **Context Declaration**: the rules for *this* space (strictness, which actions are high-impact, which receipts are required, dispute pathways, and so on).

### 7) What is a Lens?
A **Lens** is a user-facing “view configuration” over one or more contexts:
- which context(s) are in scope,
- which policies are active (safety gates, ranking gates),
- which index providers are used,
- which trust anchors are accepted,
- what visibility defaults apply.

A Lens exists so that “global discovery” does not become “global control.”

### 8) Why does WEAVE require declarations?
Because without declarations:
- people can’t consent to rules they can’t see,
- outsiders can’t audit legitimacy claims,
- and covert governance is indistinguishable from capture.

### 9) If a Context changes its rules, what happens?
It should publish a **new version of its declaration** (new artifact) plus receipts that show:
- who changed it,
- what changed,
- why,
- what dissent existed,
- and what would trigger revision.

Old versions remain referenceable (with redactions when necessary).

### 9b) What if many communities overlap in membership (people belong to multiple WEAVE contexts at once)?
That’s expected. WEAVE works best when you assume people participate in multiple contexts, as long as overlap is **explicit and scoped**.

Key principles:
- **Legitimacy is context-scoped:** roles, permissions, receipts/outcomes, and lane status are valid only within the context that issued them unless another context explicitly adopts them.
- **Lenses prevent confusion:** multi-context views are allowed, but every item must clearly indicate which context it belongs to and which policies and safety floors apply.
- **No reputation laundering by default:** authority or trust earned in Context A must not silently carry into Context B. Any cross-context trust must be explicitly declared, bounded, auditable, and revocable.
- **Privacy does not automatically widen:** overlapping membership must not cause restricted or sensitive content (members-only data, PII directories) to leak across contexts through indexing or projections.
- **Capture risks increase with overlap:** overlapping reviewer or moderator pools require stronger budgets, diversity constraints, and quarantine/membrane rules.

If two or more contexts intentionally want to share legitimacy, this should be handled as a governed decision:
- adopt an explicit alliance or trust-import charter,
- record adoption receipts,
- define scope, limits, and revocation,
- and preserve a clear exit/fork path.

# C. Meaning strictness (M0–M3) and PRISM

### 10) Do we have to use strict semantics everywhere?
No. The goal is to make ambiguity **optional**, not forbidden.

Contexts declare a meaning strictness level:
- **M0:** free talk / exploratory
- **M1:** annotated when helpful
- **M2:** grounded terms resolve to a shared lexicon
- **M3:** template-validated commitments for high-impact actions

### 11) What is PRISM’s role here?
PRISM provides:
- stable meaning identifiers (via immutable packs),
- evidence objects and selectors (durable citations),
- deterministic validation for high-impact templates,
- and removable discovery overlays (so ranking doesn’t become law).

WEAVE decides *when* PRISM strictness is required, and *who* is allowed to issue high-impact artifacts.

### 12) What’s the relationship between M-levels and PRISM validation levels?
In practice:
- M-levels are **human governance labels** (“what kind of place is this?”).
- PRISM provides the **machine-checkable validation floor** (e.g., PVL).

A context may map M-level → PRISM validation floors (for example, M2 requires grounded meanings; M3 requires templates and deterministic validation).

### 13) Why is strict meaning tied to governance?
Because a lot of abuse happens through ambiguity:
- “I didn’t mean that,”
- “you misunderstood,”
- “the policy doesn’t technically say…”

WEAVE treats semantic clarity as a safety tool, but only where it’s justified.

---

# D. Evidence-first governance: receipts, outcomes, and “no clean history”

### 14) What is a “receipt,” operationally?
A **receipt** is a durable record of a high-impact action (decision, admission, suppression, policy adoption, role assignment, etc.) that includes:
- what happened,
- who authorized it (or what roles did),
- why (rationale),
- what evidence was used,
- what dissent existed (if relevant),
- and what would trigger revision (tripwires).

### 15) What is an “outcome”?
An **outcome** is a special kind of governance artifact that changes status:
- a verdict in a dispute,
- a suppression decision,
- a restoration decision,
- a sanction or remediation requirement,
- a policy exception (time-bounded) with rationale.

In many systems: receipts are “acts”; outcomes are “verdicts.” In practice they can share a common envelope.

### 16) Why insist on receipts? Doesn’t it slow everything down?
WEAVE’s stance:
- **low-impact actions** should remain fast (no bureaucracy),
- **high-impact actions** must be accountable.

Receipts are “paperwork with purpose”: they keep communities corrigible and prevent silent coercion.

### 17) What is the “no clean history” principle?
You may redact sensitive details for safety and privacy, but you do **not** falsify the existence of:
- prior versions,
- major decisions,
- rationale trails.

This is anti-fraud: it prevents “we never said that” governance.

### 18) What about privacy — do receipts force public exposure?
No. WEAVE expects **graduated disclosure**:
- public summaries (“nutrition label”),
- restricted audit access (ombuds/panels),
- encrypted deep records, selectively disclosed when required.

### 19) Can receipts be wrong?
Yes. Receipts are “what was recorded,” not infallible truth.  
But a receipt makes error *repairable* because it leaves a trail for contestation and improvement.

---

# E. Charters, policies, and “governance gears”

### 20) What is a Charter?
A Charter is an adoptable governance module:
- binding when adopted by a Node or Context,
- forkable (with explicit diffs),
- defining rights/duties/process for a domain (markets, commons, high-impact domains, etc.).

### 21) What is a Policy (in the PRISM+NEXUS stack)?
A Policy is a machine-executed ruleset (e.g., safety gates, ranking rules, admission requirements) that NEXUS can enforce.

Charters can *require* specific policies, but:
- **Charter = governance contract**
- **Policy = executable configuration**

### 22) Why split Charters from Policies?
Because:
- Charters are about legitimacy and due process (human governance).
- Policies are about consistent execution (machine enforcement).
You need both to avoid “code is law” and also avoid “rules are vibes.”

### 23) What new Charters become useful now that PRISM+NEXUS are in play?
A few recurring ones (recommended as a library):
- **High-impact domains** (health/legal/finance/safety)
- **Evidence authenticity & provenance** (AI-era media)
- **Index provider accountability** (anti search-capture)
- **Identity/roles/key rotation** (continuity and compromise response)
- **Privacy & PII directories**
- **Fork & secession procedures**

(These are packaged in the newer “Charter Library Addendum (WCL)” concept.)

### 24) Are Charters “one size fits all”?
No. The WEAVE design expects **plurality**:
- adopt as-written,
- fork with explicit diffs,
- or adopt multiple charters as a bundle per context.

The point is legibility, not uniformity.

---

# F. Knowledge Commons operations (WKGC): lanes, admission, suppression

### 25) What is a “Knowledge Commons Context”?
A context that:
- admits content into shared memory,
- provides default discovery surfaces,
- and therefore must implement stronger anti-abuse controls than casual chat.

### 26) What are lanes and why do they matter?
Lanes are lifecycle tiers that prevent “everything is equally official”:
- **draft** (local / not default indexed)
- **proposed** (opt-in review queue)
- **admitted** (shared memory)
- **featured** (curated surface, higher bar)
- **suppressed** (hidden by default, not deleted)
- optionally **quarantine** (imports from unknown sources)

### 27) Why “suppression over deletion”?
Because deletion is the easiest way to falsify history.
Suppression means:
- it’s hidden from default views,
- but audit trails remain (with graduated disclosure).

### 28) How do proposals become admitted?
Typically:
1) proposal receipt (enters proposed lane)
2) reviewer endorsements (bounded, accountable)
3) admission receipt *or* inclusion in a signed edition/manifest
4) (optional) feature receipt for curated surfaces

### 29) Who is allowed to admit content?
Whatever the Context Declaration + adopted charters/policies say.
The important part is that admission authority is:
- declared,
- auditable,
- contestable,
- and bounded by due process.

### 30) What does “quarantine” mean in commons terms?
Quarantine is a membrane against “stream poisoning”:
- unknown imports can exist,
- but are not default-indexed,
- and are shown only under opt-in, safety-gated views until reviewed.

---

# G. Trust, influence, and service: budgets and anti-cheap-influence

### 31) What does “speech is cheap, influence is earned” mean?
It means:
- anyone may speak,
- not everyone gets equal power to shape shared memory and discovery.

Influence actions (endorse, admit, suppress, mass-contact) require budgets and accountability.

### 32) What is “service,” and why should it matter?
Service is measurable contribution (review labor, storage, relay, verification, stewardship work).  
Service prevents cheap capture by newcomers with high coordination but low contribution.

### 33) What are “budgets” in WEAVE terms?
Budgets are limits on:
- outreach,
- endorsements,
- challenges,
- other high-impact actions.

Budgets exist to prevent harassment, brigading, and attention capture.

### 34) Are budgets just “rate limits”?
No. Rate limits are one tool. Budgets are a governance concept:
- who gets influence,
- how it’s earned,
- how it decays,
- and how abuse is penalized.

### 35) What is endorsement liability?
An endorsement is not a “like.”  
If you endorse and it turns out harmful or fraudulent, your future influence may be reduced (scoped, not global shame).

### 36) Can a context still be welcoming to newcomers?
Yes:
- newcomers can propose,
- can participate in low-impact lanes,
- can earn service credits through bounded pathways.

The system should avoid “closed priesthood” dynamics.

---

# H. Safety, privacy, and graduated disclosure

### 37) What does “privacy with safety override” mean?
Default privacy is respected.  
But if someone is in danger, the governance system must have a bounded way to:
- contain harm,
- preserve evidence,
- and route to appropriate protectors/ombuds.

### 38) What is “graduated disclosure”?
A layered visibility model:
- **public label/summaries**
- **member-only details**
- **ombuds/panel audit access**
- **encrypted deep records**

This reduces coercion and voyeurism while preserving accountability.

### 39) How does WEAVE handle PII (personal data) commons?
PII commons should:
- require consent receipts,
- default to restricted visibility,
- prevent indexing of PII into public discovery surfaces,
- provide subject access and revocation pathways,
- use suppression and re-encryption rather than pretending global deletion is possible.

### 40) What does “can’t be unseen” protection mean?
It means:
- do not auto-preview risky media,
- do not autoplay,
- show metadata-only when needed,
- and treat exposure itself as potential harm.

---

# I. Discovery, indexes, and resisting “search capture”

### 41) Why does WEAVE care about search and ranking?
Because in modern systems, ranking becomes governance.
If the index is opaque, it silently decides:
- what counts as real,
- whose voice exists,
- what becomes “default truth.”

### 42) What’s the principle behind plural indexes?
Separate:
- global discovery (many indexes)
from
- global control (none).

Users should be able to choose or switch index providers, and contexts should be able to adopt/ban providers with receipts.

### 43) What is “index provider accountability”?
A governance stance that index/ranking providers must disclose:
- who they are,
- what policies they used,
- snapshots for replayability (where required),
- “why” explanations for ranking decisions.

### 44) Can “AI summaries” be part of discovery?
Yes, but they must be distinguishable from authored meaning and should carry provenance:
- what artifacts were used,
- which policies/lens was applied,
- and how the summary was derived.

### 45) How do we prevent indexes from becoming monopolies?
By design:
- portability of signed objects and outcomes,
- forkability,
- and explicit anti-capture rules (no single index can gate all participation).

---

# J. Forkability, export, and secession

### 46) Why is forkability constitutional?
Because “exit” is the ultimate anti-abuse mechanism.
If you can’t leave *with your history*, you’re trapped.

### 47) What does a real export include?
At minimum:
- the content artifacts you rely on,
- the meaning packs (or pack references) needed to interpret them,
- the receipts/outcomes needed to reconstruct legitimacy and lane status,
- and the active policy/charter references used in that context.

### 48) What is a secession procedure?
A bounded, legible way for a subset of a community to leave:
- with a declared final snapshot,
- with auditable manifests,
- and with a pathway for disputes over completeness or coercion.

### 49) Does forkability encourage fragmentation?
It can. But fragmentation is often healthier than capture.
WEAVE prefers pluralism + interoperability over coerced consensus.

---

# K. High-asymmetry governments (extra safeguards)

### 50) Are hierarchies allowed in WEAVE?
Yes, but WEAVE treats high-asymmetry structures as higher-risk and demands extra safeguards.

### 51) What extra safeguards are expected?
Examples include:
- external complaint paths without permission,
- stricter receipts scope,
- accelerated safe exit,
- periodic independent review,
- explicit limits on authority over bodies, relationships, outside contact, and dependents.

---

# L. How WEAVE evolves without a head

### 52) How does WEAVE change if there is no central authority?
Through three modes:
- adoption-by-use (most standards spread by voluntary adoption),
- stewardship of shared infrastructure (bounded steward groups),
- constitutional change (rare, slow, evidence-gated).

### 53) What is the Weave Evolution Protocol?
A procedure for evidence-gated reforms:
- proposals include motivation, failure modes, test plans, risks, receipts, tripwires,
- trials are opt-in and bounded,
- evidence packets are published with outcomes and postmortems.

### 54) Why the emphasis on trials and evidence packets?
Because governance ideas are often “true” only under certain conditions.
WEAVE tries to prevent ideology from replacing learning.

---

# M. Commons, markets, arena, and cultural questions

### 55) What is the Commons for?
To turn conversation into durable knowledge:
- extract claims,
- attach evidence,
- preserve dissent and version history,
- publish living summaries.

### 56) What is the Arena for?
To enable credible competition with integrity:
- structured challenges (“right to doubt, not to harass”),
- adjudication (including “inconclusive”),
- penalties for harassment and frivolous accusations.

### 57) What is the Agora idea (bounded markets)?
Markets can exist as contexts inside the tissue:
- inspectable,
- bounded (anti-capture),
- consentful discovery,
- receipts for high-impact deals.

### 58) Does WEAVE ban violent art or violent games?
No. The governance concern is “sacralized cruelty” — systems that glorify systematic cruelty or permanently deny agency to classes of beings.

### 59) What is “the More-Than-Human”?
A standing category for ecosystems, non-human beings under our power, future beings, and (depending on addenda) synthetic/hybrid lineages — requiring representation when stakes significantly affect them.

---

# N. Implementation: what “WEAVE-compliant” should mean in practice

### 60) What are the minimum “compliance artifacts” a Node should publish?
At minimum:
- Node Declaration (constitution version, adopted charters, complaint intake, identity/key policy, retention policy summary)
- Context Declarations for each governed context
- Public nutrition labels for each commons/market context
- Receipts/outcomes for high-impact actions
- A functioning dispute pipeline (triage → containment → investigation → decision → repair → appeal)

### 61) What should a WEAVE UI make visible?
Users should be able to see:
- what context they’re in,
- what charters are active,
- what strictness floor applies,
- what evidence supports a claim or decision,
- what receipts made a piece of content “admitted” or “suppressed,”
- and how to challenge/appeal without harassment.

### 62) What does “legible nondeterminism” mean?
If an algorithm’s output can vary, high-impact contexts should log:
- what choices were made,
- which model/version was used,
- which index snapshot was used,
so audits can replay and understand outcomes.

### 63) Can a Node claim WEAVE compliance without PRISM+NEXUS?
Yes, conceptually. WCIC is technology-agnostic.  
But PRISM+NEXUS provide practical machinery for:
- immutable declarations,
- evidence objects,
- receipts/outcomes,
- and replayable discovery.

### 64) What are common “fake compliance” failure modes?
- having “rules” but no published declarations,
- having moderators but no receipts/outcomes,
- having “appeals” but no bounded process (or retaliation),
- having ranking/search that becomes law without accountability,
- having “exit” that is social or technical hostage-taking.

---

# O. Glossary (quick reference)

- **Node:** a community that adopts WEAVE.
- **Context:** a governed space inside a node.
- **Charter:** adoptable governance module, binding when adopted.
- **Policy:** executable ruleset (safety/ranking/admission/service/index).
- **Receipt:** durable record of a high-impact action.
- **Outcome:** durable record of a verdict/state-change.
- **Lane:** lifecycle tier in a commons context (draft/proposed/admitted/featured/suppressed/quarantine).
- **Lens:** user-facing view defined by context + policy + trust anchors + index provider.
- **Graduated disclosure:** public label + restricted audit + encrypted deep record.
- **Forkability:** credible exit with history and integrity proofs.

---

## Suggested reading order (if you’re implementing)
1) WEAVE Constitution (WEAVE.md)  
2) WCIC (WEAVE-CULTURE-INTERFACE)  
3) WKGC v0.1 (Knowledge Commons Addendum)  
4) WCL v0.1 (Charter Library Addendum)  
5) PRISM v1 (meaning + evidence + validation)  
6) NEXUS v1 (execution + policy enforcement + discovery explanations)

