# WEAVE Culture Interface Charter (WCIC)
**Version:** v0.1 — Draft  
**Status:** Proposed WEAVE charter (normative, human-facing)  
**Audience:** Nodes, Context stewards, moderators/ombuds, implementers claiming “WEAVE-compliant” operation  
**Purpose:** Specify the **human/community-facing interface** that a system must provide so WEAVE governance is real: legible, contestable, repairable, and resistant to capture.

---

## 0) Relationship to CEP

WEAVE defines **what must be true** (rights, due process, truthful history, protections).
CEP (or another substrate) defines **how it is enforced** (schemas, signatures, membranes, replay).

This charter is technology-agnostic, but is written to be **mechanically implementable**.

---

## 1) The interface in plain language

To be WEAVE-compliant, a community system must make it possible to:

1. **Say what you mean when it matters** (and allow ambiguity when it’s safe).
2. **Leave receipts** for high-impact actions (decisions, evidence, dissent).
3. **Disagree without cruelty** (structured, rate-limited challenges; no harassment).
4. **Run due process** (triage → containment → investigation → decision → repair → appeal).
5. **Protect the vulnerable** (safe exit, anti-coercion, privacy with safety override).
6. **Resist capture** (earned influence, scoped trust, forkability).

---

## 2) Required public declarations

Every **Node** and every **Context** must publish a declaration.

### 2.1 Node Declaration (minimum)
- adopted WEAVE constitution version
- adopted charters (and versions/forks)
- membership cycle policy and exit rules
- ombuds / complaint intake route
- safety escalation process and emergency exit process
- policy for identity, signatures, and key recovery
- retention / historicity policy summary (“what we keep, what we summarize, what we prune”)
- default replay window declaration (how long audits can expect reconstruction to be possible)

### 2.2 Context Declaration (minimum)
A context is a specific space (topic room, market room, match room, gallery lane).

Each Context must declare:

- purpose and scope
- adopted charters and local parameters
- adopted charter hashes (`charters[]`) when operating under PRISM+NEXUS (so conformance can be checked mechanically)
- **Meaning strictness level** (M0–M3)
- which actions are “high-impact” in this context
- required receipts for high-impact actions
- dispute/challenge rules (how to doubt without harass)
- influence budgets (anti-spam / anti-brigade)
- privacy/disclosure policy (graduated disclosure)
- appeals process and limits

---

## 3) Meaning strictness: make ambiguity optional

WEAVE contexts declare a Meaning Strictness Level:

- **M0 — Free talk:** creative exploration, informal chat
- **M1 — Annotated:** tags/structure used when helpful; disagreement tolerated
- **M2 — Grounded:** important terms resolve to a shared lexicon (versioned)
- **M3 — Template:** contracts, verdicts, commitments, and safety actions use validated templates

### 3.1 The “ambiguity budget”
A context must explicitly state where ambiguity is allowed and where it is not.
Ambiguity must never be used to silence the vulnerable.

---

## 4) Receipts: evidence-first governance

For high-impact actions, the system must produce durable receipts:

- what was decided or claimed
- who participated (or what roles, if privacy requires)
- why (rationale)
- what evidence was used
- what dissent existed (positions or objections)
- what would trigger revision (tripwires)

### 4.1 “No clean history” rule
History may be redacted for safety/privacy, but the system must preserve:
- prior versions,
- change logs,
- rationale trails.

---

## 5) Contestation without harassment (“right to doubt”)

Every WEAVE-compliant context must provide:

- a structured way to file a doubt/challenge/dispute
- rate limits / budgets that prevent harassment
- penalties for abuse (frivolous or punitive challenges)
- protections for complainants and witnesses

Disputes must allow “inconclusive” outcomes; not every doubt becomes punishment.

---

## 6) Due process pipeline (always on)

Every context must support the pipeline:

1. **Triage** — is anyone in danger now?
2. **Containment** — stop ongoing harm; protect vulnerable; preserve evidence
3. **Investigation** — fair hearing; conflict-of-interest handling
4. **Decision** — verdict + rationale + remedy
5. **Repair** — restitution/service/structural change under accountability
6. **Appeal** — bounded and non-harassing

The system must make these stages **legible** and **timestamped**.

---

## 7) Identity, trust, and signatures (normative requirements)

### 7.1 Identity
A WEAVE system must support stable identities suitable for:
- accountability,
- consent,
- scoped trust,
- and safe exit.

Pseudonymity is allowed, but stability and non-repudiation for high-impact actions is required.

### 7.2 Signatures for high-impact artifacts
At minimum, the following must be attributable and tamper-evident:
- outcomes/verdicts
- policy/context changes
- certifications/commitments
- provenance manifests for published works (where relevant)

### 7.3 Key recovery and compromise
The system must publish:
- a key recovery path,
- a compromise response plan (revocation and warnings),
- a way to limit blast radius across federated nodes.

---

## 8) Trust and influence: “speech is cheap, influence is earned”

WEAVE-compliant contexts must:

- separate **agreement** from **trust** from **quality**
- compute trust as **scoped** (topic/domain/game/category), not one global score
- impose budgets on influence actions (endorsement reach, outreach, challenge velocity)
- provide transparency:
  - show raw view and trust-weighted view side-by-side
  - show “why this is trusted” explanations

---

## 9) Forkability and anti-capture guarantees

A WEAVE system must enable a community to leave capture without losing its history:

- durable objects are exportable with integrity proofs (signatures/hashes)
- competing versions can coexist; the system does not force consensus by deletion
- contexts/nodes can publish “diffs” when they fork charters or definitions

---

## 10) Privacy and graduated disclosure

Privacy is respected by default, with safety override.

A WEAVE system should support graduated disclosure such as:
- public summary (“nutrition label”)
- restricted audit access (ombuds/jury)
- encrypted deep records that can be selectively revealed when needed

---

## 11) Compliance statement (what it means to claim “WEAVE-compliant”)

A Node/Context may claim WEAVE-compliance only if it can demonstrate:

- published declarations (Node + Context)
- enforceable meaning strictness (M0–M3) appropriate to channels
- receipts for high-impact actions
- functioning dispute pipeline + due process stages
- protections for the vulnerable (exit, anti-coercion, privacy override)
- bounded influence and anti-harassment controls
- forkability/export path

---

## 12) Recommended rituals (non-binding but strong defaults)

- **Lexicon Jam:** define top disputed terms; publish versioned updates
- **Decision Review Day:** sample high-impact decisions for receipts quality
- **Replay Drill:** reconstruct an incident from receipts and produce a redacted postmortem
- **Provenance Clinic:** help creators upgrade rights/provenance manifests
- **Challenge Hygiene Drill:** practice “right to doubt” without harassment

---

## 13) Closing: the point of the interface

WEAVE is judged by whether it produces livable, corrigible communities.

This interface exists to ensure:
- power is contestable,
- history is truthful,
- dissent is treated as immune response,
- and repair is a real pathway—not rhetoric.
