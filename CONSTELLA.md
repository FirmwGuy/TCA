# Constella
People-first coordination across **Commons (public discussion)**, **Agora (market matching)**, and **Arena (leagues + matchmaking)**, built on a shared core of **relationships, culture, and trust**.

---

## 1. Summary

Constella is a distributed social system where:

- **People (personas)** are the primary anchors.
- **Claims, offers, rules, and outcomes** are durable objects.
- **Positions/endorsements** are versioned “satellites” that link people to objects.
- **Trust** is scoped (by domain/game/category) and converts participation into influence.
- **Culture** is configurable through policy packages (governance + incentives + safety).
- **Speech is cheap, influence is earned** (Sybil/AI-resistant by design).

Constella’s three flagship experiences share the same primitives:

1. **Constella Commons**: topical public discussion that yields durable, navigable knowledge.
2. **Constella Agora**: consentful provider↔customer matching (intents/offers), with stakes to price out spam.
3. **Constella Arena**: leagues and balanced matchmaking, where **integrity** is first-class and players have a **right to doubt** via structured, rate-limited challenges.

---

## 2. Design goals

### 2.1 Goals
- Preserve **knowledge and agreements** from casual conversation without forcing formal writing.
- Make **trust, accountability, and dissent** durable and auditable.
- Prevent “AI hijack” (Sybil + automation) by making **influence expensive** and **earned**.
- Enable **distributed community infrastructure** (storage, indexing, compute) with member “duties.”
- Provide **consentful discovery**: users opt into being contacted; providers can’t spam.
- Provide **fair + credible competition**: integrity is measurable; disputes are structured and adjudicated.

### 2.2 Non-goals (initially)
- Perfect anonymity with global reach and zero abuse (incompatible requirements).
- Fully P2P everything from day one (federation-first is more practical).
- “Detect AI” as a primary defense. Constella targets cheap influence, not text origin.

---

## 3. Terminology

- **Persona**: a public identity a user operates under (a user can have multiple personas).
- **Context**: a room-like container for interaction (Topic, Deal Room, Match Room are templates of Context).
- **Object**: a durable entity discussed/used inside a Context (Claim, Offer, Evidence, Summary, Result…).
- **Position**: a persona’s stance on a claim (support/oppose/uncertain/conditional), versioned.
- **Endorsement**: a typed evaluation (trust, quality, reliability, fairness) on a person/object/evidence.
- **Commitment**: a signature + optional bond/stake/certification that changes shared state.
- **Challenge**: a structured doubt/dispute/objection that triggers review.
- **Outcome**: a finalized resolution/verdict/delivery status that updates trust.

---

## 4. Core principles

1. **People are the lens; claims are the atoms.**  
   People-centric navigation, claim-centric durability.
2. **Version everything.**  
   Beliefs evolve; history should show evolution, not hide it.
3. **Separate agreement from trust from quality.**  
   “I disagree” must not imply “you are untrustworthy.”
4. **Rights come with duties.**  
   Members contribute storage/review/compute/bonds; duties taper but persist.
5. **No cheap reach.**  
   Contacting others at scale consumes scarce budgets and/or stake.
6. **Right to doubt, not right to harass.**  
   Challenges are structured, rate-limited, and abuse is penalized.
7. **Transparency by default.**  
   Show raw vs trust-weighted views; show “why this is trusted.”
8. **Forkability.**  
   Data is signed/content-addressed so communities can fork if governance is captured.

---

## 5. Architecture overview

Constella is best modeled as:

- **Append-only event log** (source of truth), plus
- **graph projections** (fast query/read models), plus
- **content-addressed blobs** (attachments, evidence, replays), stored in a community cloud.

### 5.1 Event sourcing
All user actions create signed events:
- `MessagePosted`, `ClaimCreated`, `PositionPublished`, `EndorsementGiven`,
- `ArtifactDrafted`, `ArtifactCertified`, `ChallengeFiled`, `VerdictIssued`, etc.

Events are immutable; later edits create new versions that reference the previous version.

### 5.2 Projections
Servers and clients build projections:
- Topic pages (living summaries, decision logs)
- Claim pages (support/opposition distributions, evidence)
- Matchmaking queues (tickets, constraints)
- Trust graphs (topic-scoped trust)
- Marketplace indices (intents/offers)

### 5.3 Content addressing + signatures
- Each object/event has a stable ID (UUID) and a content hash.
- High-impact objects are signed by authors and (optionally) certifiers.
- Evidence blobs are stored as encrypted, chunked content with Merkle roots.

---

## 6. Identity and personas

### 6.1 Personas
A single user can own multiple personas:
- professional persona, hobby persona, pseudonymous debate persona, etc.

Personas have:
- a public profile
- optional verified attributes (community vouches, external verification)
- scoped trust vectors (see §9)

### 6.2 Keys and signatures
- Each persona has a signing key.
- All endorsements, commitments, challenges, and verdicts are signed.
- Key rotation is supported; rotations are signed and linked.

---

## 7. Data model (implementation-oriented)

Below are **logical schemas**; implementations may use SQL + graph indices, or document stores + secondary indexes.

### 7.1 Base types (TypeScript-like)
```ts
type ID = string;            // uuid
type Hash = string;          // multihash
type Timestamp = string;     // ISO-8601
type Signature = string;

type Scope = {
  kind: "topic" | "marketCategory" | "gameMode" | "global";
  id?: ID;                   // e.g., topicId or gameModeId
  tags?: string[];
};

type VersionRef = {
  supersedes?: ID;           // previous version ID
  createdAt: Timestamp;
};
````

### 7.2 Core node types

#### Persona

```ts
type Persona = {
  id: ID;
  displayName: string;
  bio?: string;
  createdAt: Timestamp;

  // duties/trust standing
  membership: {
    status: "visitor" | "probation" | "member" | "steward" | "moderator";
    probationEndsAt?: Timestamp;
    dutyTracks?: string[];   // e.g., ["storage", "review"]
  };

  keys: {
    activeKeyId: ID;
    keyHistory: { keyId: ID; addedAt: Timestamp; revokedAt?: Timestamp }[];
  };
};
```

#### Context (Topic / Deal Room / Match Room)

```ts
type Context = {
  id: ID;
  template: "topic" | "dealRoom" | "matchRoom";
  title: string;
  description?: string;
  tags: string[];
  createdBy: ID;             // personaId
  createdAt: Timestamp;

  visibility: "public" | "members" | "private";
  governancePolicyId: ID;    // culture package
};
```

#### Claim

```ts
type Claim = {
  id: ID;
  contextId: ID;
  text: string;
  tags: string[];
  createdBy: ID;
  createdAt: Timestamp;

  // optional structure
  relatesTo?: { kind: "supports" | "contradicts" | "refines"; claimId: ID }[];
};
```

#### Evidence (generalized)

```ts
type Evidence = {
  id: ID;
  contextId: ID;
  kind: "link" | "quote" | "file" | "receipt" | "telemetry" | "replay";
  summary: string;
  uri?: string;              // for link evidence
  blobRef?: { hash: Hash; size: number; merkleRoot?: Hash };

  createdBy: ID;
  createdAt: Timestamp;

  access: "public" | "members" | "restricted"; // restricted = explicit grants
};
```

### 7.3 Relationship/edge types

#### Position (persona ↔ claim)

```ts
type Position = {
  id: ID;
  personaId: ID;
  claimId: ID;
  scope: Scope;              // often topic scoped

  stance: "support" | "oppose" | "uncertain" | "conditional";
  confidence?: number;       // 0..100
  conditions?: string;       // human-readable

  epistemicStatus?: "measured" | "experienced" | "inferred" | "hearsay" | "speculative";
  citations?: { evidenceId: ID; note?: string }[];

  version: VersionRef;
  signature: Signature;
};
```

#### Endorsement (persona ↔ target)

Targets can be Persona, Claim, Evidence, Artifact, Offer, etc.

```ts
type Endorsement = {
  id: ID;
  endorserId: ID;
  target: { kind: string; id: ID };
  scope: Scope;

  type:
    | "agree" | "disagree"
    | "trust" | "distrust"
    | "quality" | "fairSummary"
    | "sourceReliable" | "sourceUnreliable"
    | "sportsmanship" | "reliability";

  strength: 1 | 2 | 3 | 4 | 5;
  rationale?: string;
  citations?: { evidenceId: ID; note?: string }[];

  version: VersionRef;
  signature: Signature;
};
```

#### Commitment (stakes/bonds/certification)

```ts
type Commitment = {
  id: ID;
  actorId: ID;               // personaId
  contextId: ID;

  kind: "certifyArtifact" | "stakeBond" | "participationBond" | "vouchPersona";
  amount?: number;           // credits or external stake
  currency?: "credits" | "fiat" | "crypto";
  terms?: string;

  references?: { kind: string; id: ID }[]; // e.g., artifactId
  createdAt: Timestamp;
  signature: Signature;
};
```

#### Challenge (objection/dispute/integrity)

```ts
type Challenge = {
  id: ID;
  filedBy: ID;               // personaId
  contextId: ID;
  target: { kind: string; id: ID }; // claim, artifact, match, persona
  kind: "objection" | "dispute" | "integrity";
  reasonCode: string;        // taxonomy per template/policy
  notes?: string;
  evidenceRefs?: ID[];       // evidence IDs
  spentToken?: boolean;      // e.g., challenge token in Arena

  status: "filed" | "triaged" | "underReview" | "resolved" | "dismissed";
  createdAt: Timestamp;
  signature: Signature;
};
```

#### Outcome (resolution/verdict)

```ts
type Outcome = {
  id: ID;
  contextId: ID;
  kind: "resolution" | "dealOutcome" | "matchVerdict";
  status: string;            // e.g., adopted/superseded, delivered/disputed, cleared/violation
  summary: string;
  references?: { kind: string; id: ID }[]; // claim/artifact/match
  decidedBy?: ID[];          // jurors/stewards (may be hidden until later)
  createdAt: Timestamp;
  signature: Signature;
};
```

---

## 8. Culture packages (governance policy)

A **culture package** is a configuration applied per Context (and sometimes per community server).

### 8.1 Example policy parameters

* Membership:
  * probation duration
  * supported duty tracks
  * minimum duty thresholds
  
* Endorsement:
  * budgets per time window
  * whether endorsements require rationales
  * weighting rules (trust vector components)

* Publication:
  * “unanimous” vs “no strong objections” vs “threshold + dissent”
  * time windows for objections

* Dispute:
  * jury size and selection rules
  * appeal limit
  * bond requirements for filing disputes

* Safety:
  * harassment rules (public accusations allowed? default private?)
  * content visibility defaults
  * rate limits and velocity caps

### 8.2 Recommended starter packages

1. **Open Commons**: easy entry, slow influence, strong anti-brigade.
2. **Verified Arena**: stricter integrity requirements, bonds, formal review.
3. **Professional Agora**: provider stakes, dispute mediation, stronger identity options.

---

## 9. Trust kernel (scoped, multi-dimensional)

Trust should be computed as a **vector** per scope (topic/category/game-mode), even if the UI shows a simplified label.

### 9.1 Trust vector components (suggested)

* `T_accuracy(scope)`
* `T_fairness(scope)` (summarization and dispute behavior)
* `T_conduct(scope)` (harassment, toxicity, spam)
* `T_reliability(scope)` (follow-through; in Arena: leavers/AFK)

### 9.2 Trust sources

* weighted endorsements from trusted personas (with caps)
* outcomes of disputes/reviews (cleared vs violation; dispute accuracy)
* duty completion (probation; ongoing civic work)
* time and consistency (decay + recency)
* diversity (trust earned across independent clusters)

### 9.3 Sybil resistance levers

* **Probation gates**: new accounts have low endorsement budgets and low weight.
* **Budgets**: endorsements/certifications consume scarce capacity.
* **Diversity quorum**: high-impact labels require support from independent trust neighborhoods.
* **Velocity limits**: “settled” status cannot flip instantly from a brigade.
* **Graph penalties**: downweight dense mutual-endorsement rings with low external edges.
* **Raw vs weighted** displays: transparency reduces “rigged” accusations.

---

## 10. Duties and community resources (the Constella Cloud)

Members contribute civic duties; duties taper after probation but persist in lighter form.

### 10.1 Duty tracks (choose one or more)

* **Storage**: host encrypted erasure-coded shards.
* **Review**: complete calibrated review tasks (anchors, summaries, disputes).
* **Compute**: run verified jobs (indexing, trust projection, metrics).
* **Bond**: refundable stake that can be slashed for abuse.
* **Vouching**: web-of-trust (limited issuance, with responsibility).

### 10.2 Safe distributed storage

To avoid legal/safety issues:

* store only **encrypted shards** (client-side encryption)
* chunk + erasure code (n-of-k reconstruction)
* content-address (hash IDs)
* challenge-response audits for proof-of-storage
* replicate across stable “core nodes” and member nodes

### 10.3 Credits

Credits are earned by duties and spent on:

* increased replication/retention
* compute jobs
* provider outreach in Agora
* participation bonds in Arena
* premium indexing/search features (optional)

---

## 11. Constella Commons (public discussions)

### 11.1 Workflow

1. **Conversation** happens in a Topic Context.
2. Users **extract claims** from messages or draft claims directly.
3. Users publish **positions** (support/oppose/uncertain/conditional) with citations.
4. Users draft **artifacts**:
   * TL;DR, Decision log, FAQ, Open questions, Argument map
5. Community applies publication policy:
   * unanimous / no objections / threshold + dissent
6. Published artifacts enter the **Catalog** and are versioned over time.

### 11.2 Artifact model (simplified)

Artifacts are objects like:

* `SummaryArtifact`, `ResolutionArtifact`, `FAQArtifact`, `ArgumentMapArtifact`

Each artifact:

* references claim IDs and evidence IDs
* is signed by the author(s)
* can be certified by commitments
* can be challenged (objection)
* has “superseded by” pointers

### 11.3 Status labels (example)

* **Draft**
* **Proposed**
* **Adopted**
* **Contested**
* **Superseded**
* **Deprecated**

---

## 12. Constella Agora (market matching)

Agora replaces broadcast advertising with **consentful matching**.

### 12.1 Core objects

* **Intent**: what a user wants (constraints, budget, timeline)
* **Offer**: what a provider offers (terms, service area, proof)
* **Deal Room**: a Context created when both sides engage

### 12.2 Anti-spam economics

* providers must spend credits or post a bond to:
  * list offers above a threshold
  * contact intents

* repeated spam/deception:
  * bond slashing
  * reputation/trust reduction
  * outreach budget reduction

### 12.3 Deal outcomes

At the end of a deal:

* create a `dealOutcome` Outcome object:
  * delivered / not delivered / partial / disputed

* optional public redacted summary (policy-controlled)

---

## 13. Constella Arena (leagues + matchmaking + integrity)

Arena treats **integrity** as a first-class system to address cheats and “too-easy wins” distrust.

### 13.1 Core objects

* **Queue Ticket**: player’s intent to join a match (mode/role/ping constraints)
* **Match Room**: Context for a specific match (transient, but produces durable outcomes)
* **Evidence Dossier**: replay + server-authoritative logs + telemetry summaries
* **Integrity Challenge**: structured doubt/report, rate-limited by tokens
* **Verdict**: outcome of adjudication (cleared/inconclusive/violation)

### 13.2 Matchmaking goals

Optimize across:

* skill balance (team predicted win probability near 50/50)
* role composition constraints
* reliability (avoid leavers)
* integrity tier (ranked/verified queues)
* latency/region constraints

### 13.3 Right to doubt (without witch hunts)

* Any player can file an **Integrity Challenge** using limited tokens.
* Challenges are private by default (to prevent harassment).
* Repeated frivolous challengers lose tokens and rights.
* Accurate challengers maintain rights and can earn reviewer eligibility.

### 13.4 Evidence and adjudication

* Ranked/league matches automatically generate dossiers.
* Automated triage flags outliers (no punishment alone).

* Human jury review:
  * drawn from high-integrity pool
  * diversity constraints (avoid clique capture)
  * conflict-of-interest rules

* Verdict options:
  * cleared
  * inconclusive (no penalty, may require stricter queue)
  * violation confirmed (penalties)
  * malicious challenge (penalty for challenger)

### 13.5 Participation bonds (recommended)

* Ranked participation may require a small bond in credits.
* Bond is returned normally.
* Slashed for AFKs/leaving; heavily slashed for confirmed cheating.

---

## 14. Matching engine (shared by Agora and Arena)

Constella uses a general **constraint + scoring** matcher.

### 14.1 Inputs

* “Demand” objects: Intents (Agora), Queue Tickets (Arena)
* “Supply” objects: Offers (Agora), other Tickets (Arena)
* Constraints:
  * hard constraints (must satisfy)
  * soft constraints (preferences)

### 14.2 Outputs

* ranked matches with an explanation:
  * “matched due to tags X/Y, distance, availability”
  * “balanced by skill 51/49, roles satisfied, integrity tier verified”

### 14.3 Anti-abuse controls

* rate limits and budgets for creating tickets/offers/intents
* stake for provider outreach
* velocity limits (prevent sudden manipulation spikes)

---

## 15. Moderation and governance

### 15.1 Roles

* Visitor: read public; limited posting
* Probation: can participate; limited influence
* Member: can endorse; moderate influence
* Steward: can certify; can run juries/reviews
* Moderator: enforce conduct; remove spam; manage disputes

### 15.2 Appeals

* structured appeals with limited frequency
* optional bond to prevent abuse
* higher quorum or higher-trust jury for appeals

### 15.3 Forking

Because data is signed and content-addressed, communities can:

* mirror catalogs
* fork governance policies
* migrate to different servers without losing audit trails

---

## 16. Federation model (recommended: federation-first)

### 16.1 Servers and replication

* Multiple community servers participate in federation.
* Servers replicate:
  * public contexts/objects/events
  * optionally private contexts among permitted servers
* Servers can apply local moderation policies while still viewing shared objects.

### 16.2 Conflict handling

* events are append-only; conflicts occur in projections, not in the log
* for artifacts, “supersedes” links create explicit version trees
* policy defines how to present competing versions

---

## 17. APIs and event types (starter list)

### 17.1 Core API capabilities

* Create/read Contexts (topics/deals/matches)
* Post messages
* Create claims
* Publish position versions
* Endorse objects/personas
* Upload evidence blobs (encrypted)
* Draft/certify artifacts
* File challenges and read challenge states
* Issue outcomes/verdicts (role-gated)
* Matchmaking endpoints for Agora/Arena

### 17.2 Example event taxonomy

* `ContextCreated`
* `MessagePosted`
* `ClaimCreated`
* `EvidenceAdded`
* `PositionPublished`
* `EndorsementPublished`
* `ArtifactDrafted`
* `ArtifactCertified`
* `ChallengeFiled`
* `ChallengeTriaged`
* `ReviewAssigned`
* `OutcomeIssued`
* `TrustProjectionUpdated` (derived; not authoritative)

---

## 18. MVP roadmap (practical sequencing)

### Phase 1: Constella Core + Commons MVP

* Personas + signed events
* Topic contexts + message posting
* Claims + positions + anchors/evidence links
* Manual living page + basic artifact drafting
* Basic trust gates (probation, endorsement budgets)

### Phase 2: Publication + Challenges

* artifact certification (threshold/no-objections)
* objection challenges + review workflow
* raw vs weighted trust display
* simple moderation roles

### Phase 3: Agora MVP

* intents + offers + matching
* deal rooms + outcomes + disputes
* provider stakes + outreach budgets

### Phase 4: Arena MVP

* queue tickets + team matchmaking
* match rooms + basic result logging
* integrity challenges + jury review
* participation bonds + reliability metrics

### Phase 5: Constella Cloud + Federation

* encrypted shard storage + audits
* index/compute nodes (optional)
* federation for public contexts and artifacts

---

## 19. Open questions (to resolve during implementation)

* Which signature scheme and key management UX is acceptable for mainstream users?
* How much privacy vs auditability per culture package?
* How to balance explainability and anti-gaming for trust scoring?
* What is the minimal viable evidence dossier in Arena that respects privacy?
* What economic parameters (bond sizes, budgets, decay rates) keep abuse expensive but onboarding friendly?

---

## 20. One-sentence product definition

**Constella is a people-centered, trust-weighted, versioned social graph that turns interaction into durable agreements, fair markets, and credible competitive play—without letting cheap identities or automation hijack communal reality.**
