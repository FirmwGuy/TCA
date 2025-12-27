# DIYA FAQ
SPDX-FileCopyrightText: © 2025–present Victor M. Barrientos <firmw.guy@gmail.com>
SPDX-License-Identifier: CC-BY-SA-4.0
**DIYA = Do It Yourself Adventure**
**Applies to:** DIYA Conceptual Specification v0.1  
**Version:** v0.1 — FAQ  
**Status:** Draft — explanatory; not normative  
**Last updated:** 2025-12-27  

This FAQ explains what DIYA is, what problems it targets, and how its workflow and records make AI-assisted creation safer, more attributable, and more remixable.

---

## A. Orientation

### 1) What is DIYA in one sentence?
DIYA is a creative platform and community where AI is a workshop partner and where process, provenance, licensing, and versioning are first-class so creators can collaborate safely and transparently.

### 2) What problem is DIYA trying to solve?
DIYA addresses failure modes of “generate-first” creation:
- generic drift and loss of authorial intent,
- unclear rights and provenance for inputs/outputs,
- metadata stripping and unverifiable origin stories,
- collaboration without credit/consent clarity,
- privacy tension: prompts matter, but public prompts can be risky.

### 3) What does DIYA not try to do?
DIYA is not trying to guarantee:
- perfect anonymity with zero abuse,
- one-click “prove I didn’t use AI,”
- a universal legal ruling on authorship across jurisdictions.

It provides records, norms, and mechanisms so communities and downstream platforms can make more trustworthy decisions.

---

## B. The DIYA Gate and workflow

### 4) What is the “DIYA Gate” and why is it required?
Every project starts with:
- a human **Seed** (at least one creator-provided artifact),
- an **Intent Card** (goals + constraints).

This reduces blank-canvas generation drift and makes intent auditable without forcing public disclosure of everything.

### 5) What is an Intent Card?
An Intent Card is a structured statement of creative goals and constraints, typically with both:
- a private full version,
- a public summary.

### 6) What are the AI roles in DIYA?
DIYA distinguishes:
- **Assist**: critique/variants/translation/suggestions,
- **Generate**: substantial drafted output curated and transformed by the creator,
- **Train**: fine-tuning or training, which raises higher governance and rights complexity.

---

## C. Provenance, prompts, and privacy

### 7) What is DIYA’s “two-layer transparency model”?
DIYA separates:
- a **public nutrition label** (high-level disclosure for trust),
- a **private deep record** (full provenance details, optionally encrypted).

### 8) Why commit–reveal prompts?
Commit–reveal allows a creator to:
- commit to a prompt (hash/signature) at the time of creation,
- reveal it later to moderators/auditors if needed,
without making prompts universally public by default.

### 9) What is a Provenance Manifest?
A (recommended signed) record that links:
- tools/models used,
- inputs (seeds, references) and their hashes,
- process steps and transformations,
- outputs and their hashes,
with optional encrypted sections for sensitive data.

---

## D. Rights and licensing

### 10) What is a Rights Binder?
A structured record of input sources and their permissions (owned/licensed/public domain/unknown) so licensing and compliance decisions are not hand-waved.

### 11) Why does DIYA model “three rights layers”?
Because real-world creation mixes:
- rights in inputs,
- rights and terms in tools/models,
- rights and licensing for outputs and derivatives.

DIYA’s goal is to make those separations explicit enough to compute compatibility and explain decisions.

---

## E. Versioning and collaboration

### 12) How does versioning work in DIYA?
Projects are versioned with checkpoints and changelogs. Versions reference immutable, content-addressed assets so “what changed” can be inspected and verified, even when platforms strip embedded metadata.

### 13) Why is video called out as special?
Video often requires heavier provenance and versioning support (multiple assets, edits, renders). DIYA treats this explicitly rather than pretending video is “just another file.”

### 14) What is a “Commentary Work”?
A commentary work is either:
- a non-destructive overlay/annotation/critique, or
- a forked derivative work with its own version history.

### 15) How do forks and merges work?
Forks create new projects linked to parents; merges flow through merge requests with review threads, so collaboration scales without losing credit and provenance trails.

---

## F. Governance, disputes, and exports

### 16) How can disputes work if prompts are private/encrypted?
DIYA expects selective disclosure: creators can disclose private provenance to moderators/auditors under permissions, preserving privacy while enabling investigation.

### 17) How does DIYA export and interoperate?
DIYA supports:
- flat export (final media + minimal label when possible),
- sidecar export (media + signed manifest + optional encrypted payload),
- a DIYA package export (archive with assets, manifests, rights binder, intent card, signatures).

---

## G. Relationship to TCA / PRISM / WEAVE / NEXUS / PRAXIS

### 18) How does DIYA relate to the TCA stack?
DIYA is an example “upper-layer product” that benefits from TCA-style invariants (auditability, credible exit, separation of powers) without being identical to the constitutional layer itself.

### 19) Does DIYA require PRISM, WEAVE, or NEXUS?
No. But mapping DIYA records onto PRISM artifacts, governing publication with WEAVE-style receipts/outcomes, and executing discovery with a NEXUS-style runtime can make DIYA ecosystems more interoperable and harder to capture.

### 20) Where does PRAXIS fit?
PRAXIS can assist with critique, extraction, and summaries, but should remain assistive and policy-bounded, not a sovereign author or governor.
