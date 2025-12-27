# CONSTELLA FAQ
SPDX-FileCopyrightText: © 2025–present Victor M. Barrientos <firmw.guy@gmail.com>
SPDX-License-Identifier: CC-BY-SA-4.0
**CONSTELLA = Commons Offers Network — Signed Trust, Evidence, Ledgers, Lanes, Agreements**
**Version:** v0.1 — FAQ  
**Status:** Draft — explanatory; not normative  
**Last updated:** 2025-12-27  

This FAQ explains what CONSTELLA is, what problems it targets, and how it relates to the TCA stack (PRISM / WEAVE / NEXUS / PRAXIS).

---

## A. Orientation

### 1) What is CONSTELLA in one sentence?
CONSTELLA is a people-centered, trust-weighted, versioned social graph that turns interaction into durable agreements, fair markets, and credible competitive play—without letting cheap identities or automation hijack communal reality.

### 2) What problem is it trying to solve?
CONSTELLA targets failure modes where:
- speech and content creation are cheap,
- attention and reach are easily bought or botted,
- trust signals are opaque and capturable,
- disputes become harassment or endless flamewars,
- communities can’t “take their history with them” when governance fails.

### 3) What does “speech is cheap, influence is earned” mean in practice?
Influence is constrained by:
- probation gates for new personas,
- budgets for endorsements/certifications/outreach,
- diversity/quorum requirements for high-impact labels,
- velocity limits (status can’t flip instantly),
- transparency (raw vs trust‑weighted views).

### 4) What are the three flagship experiences?
They share one core set of primitives (personas, durable objects, versioned positions, endorsements, challenges, outcomes):
- **CONSTELLA Commons**: public discussion that yields durable, navigable knowledge objects.
- **CONSTELLA Agora**: consentful provider↔customer matching (intents/offers) with anti-spam economics.
- **CONSTELLA Arena**: leagues/matchmaking with integrity-first dispute resolution and a right to doubt.

### 5) Is CONSTELLA “a protocol” or “a product”?
CONSTELLA is an application-level system indication (a product family and its primitives). It can be implemented in many ways and can optionally adopt TCA/WEAVE/PRISM/NEXUS components for stronger guarantees.

---

## B. How it works (at a high level)

### 6) What is the source of truth?
An append-only, signed event log (plus content-addressed blobs for large attachments and evidence), with query-optimized projections derived from that log.

### 7) How does versioning work?
Edits are new versions that reference prior versions; the system preserves evolution instead of overwriting history.

### 8) What is a “position” vs an “endorsement”?
- A **Position** is a persona’s stance on a claim (support/oppose/uncertain/conditional), versioned over time.
- An **Endorsement** is a typed evaluation (trust/quality/reliability/fairness) about a person or object, also versioned and budgeted.

### 9) How does trust work?
Trust is computed as a vector per scope (topic/category/game-mode), sourced from endorsements, dispute outcomes, duty completion, time/consistency, and diversity.

### 10) Why “duties”?
Duties are a civic counterweight to cheap participation: members contribute storage/review/compute/bonds/vouching so the commons can resist spam, brigading, and external capture without becoming paywalled.

---

## C. Disputes, integrity, and safety

### 11) What is the “right to doubt”?
People can file structured, rate-limited challenges that trigger review. The system is designed to make dissent legible while penalizing harassment and frivolous abuse.

### 12) How are disputes resolved?
Disputes are handled through:
- challenge filing (rate-limited / optionally bonded),
- triage,
- review (often by a qualified pool with diversity constraints),
- an explicit **Outcome/Verdict** object that updates shared state and trust projections.

### 13) How does CONSTELLA handle privacy?
It aims for “public by default where safe” with selective disclosure where necessary:
- encrypted content shards for sensitive blobs,
- policy-controlled redactions/summaries,
- explicit audit trails for high-impact actions.

---

## D. Federation and forkability

### 14) Does CONSTELLA support federation?
Yes in principle: multiple community servers can replicate public contexts/objects/events, with optional private replication among permitted servers.

### 15) What does forkability mean here?
Because objects/events are signed and content-addressed, communities can export catalogs, fork governance packages, and migrate servers without losing integrity or audit trails.

---

## E. Relationship to TCA / PRISM / WEAVE / NEXUS / PRAXIS

### 16) How does CONSTELLA relate to TCA?
TCA is a constitutional layer that defines invariants (separation of powers, auditability, credible exit, UI non-sovereignty). CONSTELLA is an example application that aligns naturally with those invariants.

### 17) Does CONSTELLA require PRISM?
No. But mapping CONSTELLA’s durable objects (claims/evidence/outcomes) onto PRISM artifacts can provide stronger content-addressing, explicit meaning, and verifiability grades.

### 18) Does CONSTELLA require WEAVE?
No. But adopting WEAVE as a governance profile can provide legible declarations, receipts/outcomes, due process, and charter modularity that make CONSTELLA-style systems harder to capture.

### 19) Does CONSTELLA require NEXUS?
No. But NEXUS is a natural execution engine for canonical artifacts and policy-executed derived discovery/projections, which fits CONSTELLA’s need for transparent search, ranking explanations, and replayable governance-visible state.

### 20) Where does PRAXIS fit?
PRAXIS can assist with analysis (triage, extraction, summaries) without becoming a sovereign governor. In TCA terms, intelligence remains assistive and bounded by policy.

### 21) Is CONSTELLA related to WEAVE “Harm‑Response Constellations”?
No. WEAVE’s “Constellation” is a governance role-pattern for distributing high-trust responsibilities. CONSTELLA is a separate system name and acronym.

---

## F. Implementation

### 22) What is a reasonable MVP?
An MVP can start with:
- personas + signed events,
- topic contexts + posting,
- claims + position versions,
- basic trust gates (probation and budgets),
- challenge + review + outcome objects,
then expand into Agora and Arena workflows.
