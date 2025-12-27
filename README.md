# The Trust Commons Accord (TCA)

TCA is a **constitution** for building systems where shared meaning, governance legitimacy, execution, interfaces, and intelligence remain separable, auditable, and forkable.

This repo is a **pure specification** repository: it contains no reference implementation code.

---

## Why this exists

Today, the internet is very good at producing content and very bad at producing **trust**.

When content is cheap and abundant (and now machine-generated at industrial scale), the scarce resource is the ability to **rely**:

- Rely on what a word means in a high-stakes discussion.
- Rely on how decisions were made (and who made them).
- Rely on what you’re seeing (and what you’re not seeing).
- Rely on the history not being silently rewritten.
- Rely on being able to leave without losing your work and your history.
- Rely on knowing when “the feed” (ranking and UI) is acting like law.

TCA is an attempt to give communities and builders a cleaner foundation: not “perfect truth”, but **auditable legitimacy**, **explicit meaning when needed**, and **credible exit** when governance fails.

---

## What is TCA (plain language)?

Think of TCA as a constitution for building “commons systems”:

- knowledge commons (shared claims + evidence),
- creative commons (process + provenance + remix),
- markets and matching (consentful offers/intents),
- communities and leagues (trust + integrity + dispute resolution).

TCA says the system must be designed so that key kinds of power don’t get mixed together and captured:

- **Meaning**: what claims and terms mean.
- **Governance**: what rules apply, who can act, and how decisions are made.
- **Execution**: storage, indexing, search, rendering, and enforcement of declared policies.
- **Interface**: the UI and ranking (which can quietly become the real governor).
- **Intelligence**: assistive tools (summaries, extraction, triage) which must not become unaccountable rulers.

If you’re not technical: you can read these documents like a constitution and a set of operating rules. The goal is *legibility*.

---

## A simple example (how this feels)

Imagine a public knowledge commons where people want to build a shared record without turning it into a propaganda feed:

1. Someone publishes a claim and links evidence for it.
2. The community publishes its rules (a “context declaration”) so everyone can see what standards apply.
3. If a high‑impact action happens (admission, suppression, bans, policy changes), it leaves a durable “receipt/outcome” so history can’t be silently rewritten.
4. Search results and pages are **derived views**: they’re computed from the canonical record and the declared rules, and they carry provenance for audit.
5. If the community is captured, people can export and fork without losing integrity or the decision trail.

TCA is the “constitution” for building systems that can support that kind of behavior.

---

## A few words you’ll see

- **Claim**: a single atomic statement (the system treats it as an object you can reference and debate).
- **Evidence**: a source, excerpt, media, or provenance trail that supports or contests a claim.
- **Context**: a scope where a particular set of governance rules applies (a community, room, domain).
- **Receipt/Outcome**: a durable record that a high‑impact governance action happened (who/what/why).
- **Policy**: a deterministic configuration/enforcement artifact executed by the runtime (not “legitimacy” by itself).
- **Canonical vs derived**: canonical artifacts are the durable record; derived outputs are computed views (search indexes, pages, rankings) that must remain explainable and replayable.

---

## The stack (how the pieces relate)

TCA is the floor. Other documents describe layers that can satisfy it:

- `prism/PRISM.md` — **PRISM**: durable artifacts for meanings, claims, and evidence (with verifiability levels).
- `weave/WEAVE.md` — **WEAVE**: a governance profile (receipts/outcomes, due process, charters).
- `nexus/NEXUS.md` — **NEXUS**: a runtime that stores canonical artifacts and computes derived experiences (search and pages) by executing declared policies.

Some stack components are referenced but not yet fully specified here (e.g., CEP Substrate, PRAXIS).

---

## Acronyms (quick)

- **TCA**: The Trust Commons Accord
- **PRISM**: Protocol for Reproducible Interchange of Statements & Meanings
- **WEAVE**: Web of Evidence, Accountability, Versioning, Exit
- **NEXUS**: Neutral Engine for eXecutable, User‑Scoped Search
- **DIYA**: Do It Yourself Adventure
- **CONSTELLA**: Commons Offers Network — Signed Trust, Evidence, Ledgers, Lanes, Agreements

---

## Suggested reading order (non-technical)

1. `tca/TCA-MANIFESTO.md` (why the problem matters)
2. `tca/TCA.md` (the constitutional commitments)
3. `tca/TCA_FAQ.md` (plain-language clarifications)
4. `weave/WEAVE.md` and `weave/WEAVE_FAQ.md` (how governance becomes real)
5. `nexus/NEXUS.md` and `nexus/NEXUS_FAQ.md` (how an engine can execute governance + produce explainable discovery)
6. `prism/PRISM.md` and `prism/PRISM_FAQ.md` (how meaning/claims/evidence become durable objects)

If you’re implementing a knowledge commons:

- `weave/WEAVE_WKGC.md` and `weave/WEAVE_WCL.md` (operational governance: lanes, receipts, charters)
- `nexus/NEXUS-POLICIES.md` (normative policy schemas)
- `nexus/PSR.md` (deterministic rendering)
- `nexus/PSA.md` (candidate extraction into review lanes)

---

## What’s in this repo (document map)

- `tca/TCA.md` — the TCA constitutional text (non‑negotiable invariants)
- `tca/TCA_FAQ.md` — TCA FAQ (explanatory)
- `tca/TCA-MANIFESTO.md` — manifesto / narrative framing (explanatory)

- `prism/PRISM.md` — PRISM spec (meaning + verifiability artifacts)
- `prism/PRISM_FAQ.md` — PRISM FAQ

- `weave/WEAVE.md` — WEAVE governance profile
- `weave/WEAVE-WCIC.md` — WEAVE Culture Interface Charter (human-facing interface requirements)
- `weave/WEAVE_WKGC.md` — WEAVE addendum: Knowledge Commons Governance for PRISM + NEXUS (WKGC)
- `weave/WEAVE_WCL.md` — WEAVE addendum: Charter Library for PRISM + NEXUS Commons (WCL)
- `weave/WEAVE_FAQ.md` — WEAVE FAQ (expanded, explanatory)

- `nexus/NEXUS.md` — NEXUS runtime spec (policy-executing store/query/discovery/projections/ingestion)
- `nexus/NEXUS-POLICIES.md` — normative policy artifact schemas executed by NEXUS
- `nexus/PSR.md` — deterministic rendering spec (PRISM Surface Realizer)
- `nexus/PSA.md` — candidate extraction spec (PRISM Semantic Analyzer)
- `nexus/NEXUS_FAQ.md` — NEXUS FAQ (explanatory)

- `diya/DIYA.md` and `diya/DIYA_FAQ.md` — DIYA (creative trust) as an example upper-layer product
- `constella/CONSTELLA.md` and `constella/CONSTELLA-FAQ.md` — CONSTELLA (social trust) as an example upper-layer product

Some components referenced by the docs (e.g., CEP Substrate, PRAXIS) are part of the wider stack concept but are not yet specified in this repo.

---

## Change history

See `CHANGELOG.md`.

---

## License

This repository is licensed under **Creative Commons Attribution‑ShareAlike 4.0 International (CC BY‑SA 4.0)**. See `LICENSE`.
