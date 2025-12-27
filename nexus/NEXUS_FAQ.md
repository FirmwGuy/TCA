# NEXUS FAQ
**NEXUS = Neutral Engine for eXecutable, User‑Scoped Search**
**Applies to:** NEXUS v1.0  
**Companion specs:** PRISM v1.0, PSR v0.1, NEXUS-POLICIES v0.1, WEAVE (Constitution + WCIC), WKGC v0.1, WCL v0.1  
**Last updated:** 2025-12-26

NEXUS is the execution engine for an online, governance-aware claim graph knowledgebase. This FAQ explains what NEXUS is, how it relates to PRISM/WEAVE/PSR, and how to implement or operate it.

For normative policy schemas (Safety/Ranking/Quality/Admission/Service/Index/Projection/Ingestion), see `nexus/NEXUS-POLICIES.md`.

---

## A. Orientation

### 1) What is NEXUS in one sentence?
NEXUS is a **policy-executing store + query + discovery + page-projection runtime** for a PRISM claim graph governed by WEAVE.

### 2) What does NEXUS do that a normal database doesn’t?
NEXUS:
- stores **content-addressed canonical artifacts** (PRISM),
- validates **verifiability** (PVL) over dependency closure,
- computes **governance-derived status** (lanes, attested/contested) from receipts/outcomes,
- executes a **transparent discovery pipeline** (safety → PVL → ranking → explanation),
- builds **online pages** (dictionary/encyclopedia/news digest) as derived projections with provenance.

### 3) What does NEXUS not do?
NEXUS does not:
- define meanings (PRISM packs do),
- decide legitimacy/authority (WEAVE does),
- define truth,
- require a single network protocol for replication.

### 4) Is NEXUS “the website”?
NEXUS is the **engine** behind a website (or local app). It can serve HTTP pages/APIs, but it can also be embedded as a library.

---

## B. The stack: PRISM, WEAVE, PSR

### 5) How does NEXUS relate to PRISM?
PRISM defines canonical artifacts:
- packs (meanings),
- claims,
- evidence chains,
- derivations,
- signatures/hashes,
- PVL validation rules.

NEXUS stores these artifacts, validates them, and builds derived views.

### 6) How does NEXUS relate to WEAVE?
WEAVE defines governance:
- contexts and their declarations,
- policies adopted by contexts,
- receipts/outcomes that establish status and lane membership,
- dispute and appeal pathways.

NEXUS executes WEAVE governance inputs but does not invent them.

### 7) How does NEXUS relate to PSR?
PSR (PRISM Surface Realizer) is the deterministic renderer used by NEXUS to produce human-readable pages from a bounded, policy-selected claim set.

PSR is:
- non-AI,
- deterministic,
- template/grammar driven,
- “language filter only.”

### 8) Is AI required for NEXUS?
No. NEXUS must support deterministic page rendering via PSR. Optional AI rendering can be added as a derived layer, policy-controlled, and must fall back to PSR if it fails constraints.

---

## C. Core concepts

### 9) What is a “claim graph” in NEXUS?
It’s a derived graph built from PRISM `Claim` artifacts (and optionally bundled Graph artifacts). Nodes represent instances; edges represent claims; attributes include qualifiers, time scope, evidence summaries, and governance status.

### 10) What is PVL and why does NEXUS care?
PVL (PRISM Verifiability Level) measures what can be mechanically verified about a claim and its dependencies. NEXUS reports:
- declared PVL (what the publisher claimed),
- validated PVL (what NEXUS can confirm given available dependencies).

PVL is not a truth score.

### 11) What’s the difference between “declared PVL” and “validated PVL”?
Declared PVL is what the artifact says.
Validated PVL is what a validator can confirm now:
- pass/fail/unknown (unknown usually means missing dependencies).

NEXUS should expose both and the failure reasons.

### 12) What are “lanes”?
Lanes are lifecycle tiers for content in a commons context:
- draft, proposed, admitted, featured, suppressed (and optionally quarantine).

Lane membership is computed from receipts/outcomes and/or edition inclusion, not from mutable flags.

### 13) What is a Lens?
A Lens is a view configuration that determines:
- which contexts apply,
- which policies are active,
- which lanes are visible by default,
- which index providers are allowed,
- user safety settings (cannot bypass context safety floor).

### 14) What is “canonical vs derived” and why is it important?
Canonical artifacts are authoritative bytes.
Derived products (indexes, rankings, summaries, pages) are projections that must remain discardable and provenance-tagged.

This prevents:
- “search became law,”
- “summaries became facts,”
- “rewriting history through UI.”

---

## D. Evidence, link rot, and adversarial media

### 15) Why doesn’t NEXUS treat URLs as evidence?
URLs are locators, not durable evidence:
- links rot,
- platforms change,
- content can be altered,
- access can be unequal.

NEXUS treats evidence as content-addressed PRISM EvidenceSubstrate artifacts plus selectors/derivations.

### 16) How does NEXUS handle AI-generated fake media?
NEXUS does not assume media is authentic. It:
- stores hashes and provenance,
- records derivation pipelines,
- surfaces contestation/disputes as first-class,
- uses safety gating and preview controls (“can’t be unseen”).

### 17) Does NEXUS require storing evidence bytes?
Not always. It can store substrate artifacts with hashes and optional locators; bytes may be cached locally or mirrored by policy. Availability affects validated PVL.

---

## E. Discovery: search and ranking

### 18) What is the NEXUS discovery pipeline?
A standard pipeline:
1) candidate generation (index provider)
2) safety gating (hard)
3) PVL/validity gating (hard, policy-controlled)
4) ranking/scoring (soft)
5) caps/diversity (soft)
6) explanation + provenance (required)

### 19) Why safety before ranking?
Because ranking can’t “undo exposure.” If something shouldn’t be shown, it must be filtered before relevance scoring.

### 20) Can NEXUS use embeddings or vector search?
Yes, but it must be transparent and policy-controlled:
- model identity and index snapshot must be known,
- forbidden features must be ignored,
- explanations must be produced (as far as feasible).

### 21) What prevents “search capture”?
Several mechanisms:
- contexts adopt IndexPolicy and RankingPolicy with allowlists/denylist,
- index providers must be disclosed and can be banned by receipts,
- replayable snapshots (where required),
- plural indexes and lenses so no one provider is “the truth.”

### 22) Does NEXUS allow popularity-based ranking?
It can, but WEAVE-style commons strongly discourage popularity as authority. If used, it should be:
- explicitly declared in policy,
- bounded, local, and contestable,
- never a hidden default.

---

## F. Projections: online pages from the semantic graph

### 23) What is a “projection”?
A projection is a derived page view (meaning/entity/event/digest) built from:
- a bounded selection of claims/evidence/status,
- under a lens and projection policy,
- rendered deterministically by PSR (or optionally by AI with strict constraints).

### 24) Why do projections need a policy?
Because “how far to expand,” “what is important,” and “what is safe to show” are governance questions.

ProjectionPolicy defines:
- graph depth, predicate allowlists,
- budgets per section,
- PVL floors,
- lane defaults,
- importance tier rules,
- renderer allowlists,
- neutrality constraints.

### 25) What is PSR doing exactly?
PSR takes a RenderBundle (prepared by NEXUS) and produces readable text using a template library.

PSR never:
- chooses what to include,
- infers importance,
- invents facts.

It only realizes what NEXUS selected.

### 26) If we add AI summaries, how do we keep them from “having opinions”?
Treat AI as optional rendering:
- it may only rewrite selected claims,
- it must produce sentence/paragraph-level citations to claim hashes,
- it must not introduce uncited factual statements,
- it must pass deterministic validators (schema + forbidden lexemes),
- and it must fall back to PSR when it fails.

### 27) How does NEXUS decide what’s “important” on a page?
By policy-driven, explainable signals:
- lane/status (featured/admitted),
- predicate kinds (definitional vs incidental),
- evidence strength (PVL, chain count, selector precision),
- dispute gravity (contested outcomes),
- bounded local centrality (within the selected subgraph).

NEXUS then assigns importance tiers; PSR maps tiers to longer/shorter templates.

### 28) How does NEXUS know how far to traverse the graph?
It doesn’t “wander.” ProjectionPolicy sets:
- max depth per section,
- predicate frontier allowlists,
- budgets,
- stop conditions (deterministic).

---

## G. Privacy, encryption, and rendering online content

### 29) How can NEXUS render pages if content is encrypted?
By layered disclosure:
- public pages can be metadata-only or blurred,
- authorized lenses can decrypt and render locally or in an authorized session,
- indexes must not leak decrypted payload into public search.

NEXUS should always respect visibility and safety policies.

### 30) How do we prevent PII from leaking into indexes or projections?
Policies must enforce:
- PII payloads are not tokenized into public indexes,
- directory pages default to metadata-only unless authorized,
- previews are gated and non-autoplay,
- consent receipts and encryption are required for sharing.

---

## H. Federation, editions, and forkability

### 31) What is an Edition and why does NEXUS care?
An Edition is a signed manifest listing artifact hashes. It’s a stable unit for:
- federation,
- auditing,
- replayability,
- exporting/forking.

### 32) Can NEXUS operate without federation?
Yes. It can be local-only. Federation improves redundancy and collaboration but is not required.

### 33) What does “forkability” mean operationally?
You can export:
- the content artifacts,
- the meaning packs needed to interpret them,
- the receipts/outcomes needed to reconstruct status,
- and the adopted policies/charters references,
so another node can reconstruct the commons view.

---

## I. Implementation guidance

### 34) What should I implement first?
A minimal useful NEXUS:
1) Artifact store (hash verify)
2) Pack resolution (MeaningRefs)
3) Claim ingestion + graph materialization
4) PVL-2 validation reporting
5) Basic query API
6) Deterministic projections via PSR for one page kind (entity_page)

Then add:
- governance receipts/status,
- search pipeline,
- index snapshots,
- more projection kinds.

### 35) What is a common failure mode?
- treating derived pages/summaries as canonical truth,
- hiding which policies/rules were used,
- letting proposals leak into default discovery,
- indexing sensitive payload into public search,
- equating PVL with truth.

### 36) How do we test determinism?
- fix a corpus of artifacts + packs,
- fix a lens + policy set,
- fix a template library,
- rebuild projections and ensure byte-identical outputs,
- rebuild index snapshots and ensure stable replay outputs (within declared windows).

### 37) What should a NEXUS UI always show?
At minimum:
- context(s) in view,
- lane/status (admitted/featured/contested),
- safety bucket (allow/warn/blur/metadata),
- PVL declared and validated,
- “why shown / why ranked” explanation,
- links to receipts/outcomes and evidence chains.

---

## J. Ingestion and claim extraction with PSA

### 38) What is PSA?
PSA (PRISM Semantic Analyzer) is the *opposite* of PSR. Where PSR renders structured semantics into readable text, PSA converts user-authored text (reports, writings, notes) into **candidate semantic structures**:
- candidate claims (subject / predicate / object),
- candidate entities and resolution suggestions,
- candidate meaning/predicate mappings,
- and **evidence selectors** that point back to the exact source text spans.

PSA outputs are proposals for review — not “facts.”

### 39) If PSA outputs candidate claims, are those canonical PRISM Claims?
Not by default. PSA produces a **CandidateBundle** (or equivalent) that is **non-authoritative** and is meant to be reviewed and then *published* as PRISM Claim artifacts through an explicit step.

In commons contexts, automatic extraction should route to **proposed/quarantine**, not admitted.

### 40) Why is the source text stored first as evidence?
Because every extracted candidate must be auditable. NEXUS should store the original text as a content-addressed **EvidenceSubstrate**, then PSA attaches **TextSpanSelectors** (or similar selectors) so reviewers can see exactly what phrase supported each candidate.

This prevents “facts without receipts” and makes later disputes resolvable.

### 41) Does PSA have to be an AI model?
No. PSA can be:
- **deterministic** (rules/patterns/grammars), which is easier to replay and audit, or
- **model-assisted**, which may propose better candidates but must declare nondeterminism.

Either way, PSA must be constrained to proposing structure, not deciding truth or admission.

### 42) How do we prevent PSA from “inventing facts”?
By construction:
- PSA must anchor each CandidateClaim to at least one selector in the evidence substrate.
- PSA should preserve ambiguity via alternatives (multiple candidate predicate meanings, multiple entity matches) rather than forcing one mapping silently.
- CandidateBundles must be **reviewed** before publishing into shared memory lanes.

If you want “safe-by-default,” configure PSA to prefer **attribution** when ambiguous (e.g., “Document D states X” rather than asserting X as world fact).

### 43) Where do the rules for PSA live?
In NEXUS, ingestion should be governed by an **IngestionPolicy** that can specify:
- whether PSA is enabled for a context,
- allowed PSA modes/tools/models,
- meaning pack allowlists (meaning supply chain),
- output limits (max candidates, max excerpt size),
- routing defaults (proposed vs quarantine),
- and whether any auto-publish is allowed (usually disabled).

### 44) How do CandidateClaims become PRISM Claims in NEXUS?
Through an explicit publish step (manual or policy-controlled) that:
- converts CandidateClaims into PRISM Claim artifacts,
- preserves the evidence selector chain back to the EvidenceSubstrate,
- preserves qualifiers like time and uncertainty when present,
- records conversion as a Derivation (recommended),
- and obeys AdmissionPolicy and PVL floors for the destination context/lane.

This keeps ingestion scalable while preserving governance and auditability.

## K. Glossary (quick)

- **Artifact:** content-addressed PRISM object.
- **Claim:** atomic assertion.
- **Evidence chain:** substrate/selector/derivations supporting a claim.
- **PVL:** verifiability level (0–3).
- **Context:** governed scope.
- **Receipt/Outcome:** governance record and decision.
- **Lane:** lifecycle tier.
- **Lens:** view configuration.
- **Projection:** derived page.
- **PSR:** deterministic renderer.

---
