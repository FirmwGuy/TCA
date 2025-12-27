# DIYA (Do It Yourself Adventure)
## Conceptual Specification v0.1

DIYA is a creative platform and community centered on **human intent + human seed + iterative craft**, where AI is used as a **workshop partner** (critique, variants, translation, refinement) rather than a replacement for authorship. DIYA’s core innovation is making **process, provenance, licensing, and versioning** first-class—so creators and platforms can collaborate safely and transparently.

This document is conceptual: it defines goals, norms, and system behaviors so the project can later be implemented in software.

---

## 1) Purpose and Values

### 1.1 Purpose
Build a community and tooling that enables people to:
- start with an original human idea or artifact,
- iteratively develop it using AI-assisted workflows,
- publish results with clear provenance and licensing,
- collaborate via forks, commentary, and merges,
- maintain a versioned creative history (including video).

### 1.2 DIYA Values
- **Intent-first:** meaning originates from the human creator.
- **Seed-first:** every project begins with human input (text, sketch, audio riff, etc.).
- **Process-respect:** iteration and refinement are recognized and rewarded.
- **Credit and consent:** attribution and remix permissions are explicit.
- **Verifiable provenance:** record “what went in, what came out, how it changed.”
- **Privacy by design:** prompts and sensitive inputs can be committed/verifiable without being publicly exposed.

---

## 2) Definitions and Terminology

### 2.1 Core Terms
- **Seed**: a creator-provided artifact that anchors authorship (text draft, sketch, melody snippet, photo, etc.).
- **Intent Card**: a structured statement of creative goals and constraints; a north star for iteration.
- **AI Role**:
  - **Assist**: critique, rewrite suggestions, translation, ideation variants, technical checks.
  - **Generate**: AI produces substantial drafts/assets which are then curated and transformed.
  - **Train**: user trains/fine-tunes a model (highest governance and rights complexity).
- **Rights Binder**: a record of input sources and their permissions (owned, licensed, public domain/CC, unknown).
- **Provenance Manifest**: signed record linking output to tools, inputs, process steps, and (optionally encrypted) prompts.
- **Commentary Work**: a non-destructive overlay/annotation/critique or a forked derivative work.

### 2.2 Design Assumptions
- Laws and vendor tool terms evolve. DIYA should store **policy snapshots** used at the time of generation/publishing.
- Many platforms and tools strip metadata. DIYA must treat provenance as a **first-class record**, not merely embedded EXIF.

---

## 3) High-Level System Concept

DIYA consists of:
1. **Creative Workspace**: authoring, iteration, AI-assisted workflows, version history.
2. **Provenance & Rights Layer**: manifests, commitments, signatures, licensing compatibility checks.
3. **Versioning System**: semantic versioning for creative projects; includes special handling for video.
4. **Community Layer**: galleries, critique, commentary, forks/merges, challenges, governance.

---

## 4) The DIYA Creative Workflow

### 4.1 The DIYA Gate: Seed + Intent (Required)
Every DIYA project begins with:
- **Seed v1**: at least one human-provided artifact.
- **Intent Card v1**: structured intent and constraints.

**Rationale:** prevents “blank-canvas generation drift” that can converge on generic or recognizable patterns.

### 4.2 Intent Card (Template)
Minimum fields:
- Theme / claim (what it’s about)
- Emotional target (what it should feel like)
- Voice / POV (who speaks, tone)
- Constraints (meter, palette, duration, style constraints without naming living artists)
- Must include (motifs, symbols, phrases)
- Must avoid (clichés, brands, named artists, characters, risky tropes)
- Audience / use case (personal, public, client, commercial)
- Success test (how you’ll know it worked)

DIYA should support:
- **Private Intent Card** (full details)
- **Public Intent Summary** (shareable subset)

### 4.3 Iteration Loop (Core)
A DIYA iteration is a repeatable cycle:

1. **Request** (to AI or to self):
   - critique, variants, translation, structural edit suggestions, etc.
2. **Output Set**:
   - multiple variants encouraged (“5-variant rule”).
3. **Selection Notes**:
   - user chooses and explains why (this is authorship).
4. **Human Transformation Step**:
   - user edits materially (rewrite, paint-over, recompose, re-harmonize).
5. **Verification Pass**:
   - similarity/trademark sanity checks; rights/inputs completeness.

Each iteration produces a **new version** in the project history.

---

## 5) Provenance, Prompts, and Privacy

### 5.1 Two-Layer Transparency Model
DIYA should separate:
- **Public “Nutrition Label”** (always readable)
- **Private Deep Record** (encrypted, permissioned, auditable)

#### Public Nutrition Label (examples)
- AI used: yes/no (assist/generate/train)
- Tool/model: name + version (as available)
- Human seed: present (hash or reference)
- Provenance: verified (signed)
- Prompt: open / committed / private

#### Private Deep Record
- full prompt + negative prompt
- salt used for commitment hash
- reference hashes
- internal notes
- client brief (optional)
- full intent card (optional)

### 5.2 Commit–Reveal for Prompts
To avoid forcing public prompt disclosure while preserving auditability:

- Store **prompt commitment hash** publicly:
  - `commit = hash(canonical_prompt + random_salt)`
- Store `(canonical_prompt, salt)` encrypted.
- Later, creator can selectively reveal to prove the commitment matches.

### 5.3 Signatures and Integrity
Encryption hides content; it does not prove integrity.
DIYA should **sign** provenance records to ensure:
- the record was created at the stated time,
- it was not tampered with,
- it is bound to the output asset (via content hash).

### 5.4 Provenance Manifest (Conceptual Fields)
Minimum:
- asset hash + type + timestamp
- tool name + model/version + settings (as available)
- human seed hash(es)
- intent card hash (public or private)
- prompt commitment hash
- encrypted payload (optional)
- signatures (creator, optionally platform)

---

## 6) Copyright Notices, Licensing, and Tool Terms

### 6.1 Three Rights Layers (Must Be Modeled Separately)
- **Copyright claim/status** (jurisdiction-dependent)
- **Distribution license chosen by creator** (e.g., All rights reserved, CC variants, client license)
- **Tool/vendor terms constraints** (may restrict commercial use or require attribution/share-alike)

### 6.2 License Compatibility Engine (Platform Behavior)
At publish/export time, DIYA should:
- evaluate input licenses (Rights Binder),
- evaluate tool policy constraints (policy snapshot),
- compute **allowed licenses** for the output,
- block incompatible choices and explain why.

### 6.3 Rights Binder (Input Tracking)
Each input asset is categorized:
- A: owned by creator
- B: licensed (with terms)
- C: public domain / CC (with terms)
- D: unknown/unlicensed (disallow for public or commercial release)

---

## 7) Versioning Model

DIYA versioning should be **semantic**, not file-diff-only.

### 7.1 Universal Concepts
- **Project**: the creative work container (poem, design, song, video essay).
- **Version**: a named checkpoint with a changelog.
- **Branch**: alternative direction (“director’s cut”, “rhymed version”, “minimalist version”).
- **Merge**: incorporate changes from another branch (often from a commentary work/fork).

### 7.2 Text Versioning
- Use line/paragraph diffs.
- Preserve “variant sets” as first-class artifacts.
- Allow “compare versions” with highlighted changes and intent drift notes.

### 7.3 Image Versioning
- Store as immutable assets (content-addressed).
- Track transformations:
  - crop, color grade, paint-over, composite, upscales
- Prefer semantic “edit steps” over binary diffs.
- Optionally store layered formats (where feasible) or step parameters.

### 7.4 Audio Versioning
- Store immutable takes and stems.
- Track timeline edits and mix parameters:
  - trims, fades, EQ settings, levels, effects
- Output renders are derived artifacts.

### 7.5 Video Versioning (The Hard Case)
Git-style diffing of encoded video is not practical. DIYA should version **the edit graph**.

#### Canonical Video Versioning Model
- **Immutable sources**: raw video clips, VO takes, music stems, overlays.
- **Timeline project**: cuts, in/out points, track layout, overlays, captions, effects parameters.
- **Derived renders**:
  - proxy previews
  - published exports

This makes “versions” small and meaningful (timeline deltas), even if final renders are large.

#### Optional Optimization: Segment-Based “Patches” for Published Renders
To avoid re-uploading/downloading full videos for small updates:
- publish video as **segments** (fixed duration, forced keyframes/IDR at boundaries, closed GOP)
- store segments content-addressed
- a new published version updates:
  - the manifest (segment list)
  - only the changed segments

This is an efficient delivery mechanism, while the canonical history remains the semantic timeline.

---

## 8) Commentary Works and Collaboration

DIYA supports two main collaboration modes:

### 8.1 Non-Destructive Commentary (Overlay Mode)
- timed annotations
- critique notes (structured)
- translations/subtitles
- callouts/highlights

These do not alter the original asset; they create a linked commentary object.

### 8.2 Fork + Merge (Derivative Mode)
- fork inherits provenance and license constraints
- fork can propose a merge (like a pull request):
  - “tighten intro cut”
  - “replace VO line 3”
  - “improve rhyme scheme”
- original creator can accept or reject changes
- credits propagate automatically

### 8.3 Structured Critique UI (Culture Feature)
Encourage high-quality feedback via prompts:
- what matches intent?
- what drifts from intent?
- one concrete improvement
- one question to the creator
- optional “patch proposal” (creates a branch)

Reward critique quality (reputation), not just likes.

---

## 9) Community Model (DeviantArt-Inspired)

### 9.1 Identity: “Show Your Work”
DIYA’s cultural differentiator:
- process is visible and valued
- provenance is normal
- remixing is consent-based and credited

### 9.2 Publishing Lanes
- **Sketchbook**: informal WIP, high iteration, lower feed amplification
- **Gallery**: curated/presented; requires minimum provenance + license selection

### 9.3 Discovery and Filtering
Users can filter by:
- human-only
- AI-assisted (verified seed + intent)
- fully generated (if permitted)
- provenance verified
- commercial-safe (license compatible + verified provenance)

### 9.4 Events and Rituals
- monthly challenges based on constraints (“octosyllabic week”, “one-color poster challenge”)
- critique circles
- collaborative “fork jams” with explicit remix permissions

---

## 10) Governance and Safety

### 10.1 Policy Clarity
DIYA should define clear rules for:
- impersonation and false authorship claims
- brand/IP prohibited content categories
- style mimicry policies (explicitly define what’s acceptable)
- harassment and plagiarism accusations handling

### 10.2 Dispute Resolution with Selective Disclosure
Because prompts and records may be encrypted:
- provide a mechanism for creators to disclose private provenance to moderators/auditors under permissions
- preserve creator privacy while enabling investigation

### 10.3 Anti-Spam Culture
Prevent “volume dumping”:
- rate limits for publishing to Gallery
- require process fields for Gallery posts
- reputation-based posting privileges

---

## 11) Data Model (Conceptual Entities)

### 11.1 Entities
- **User**
- **Project**
- **Asset** (immutable, content-addressed)
- **Version** (checkpoint + changelog + links to assets)
- **Intent Card** (public summary + private full)
- **Rights Binder** (input sources + licenses)
- **Provenance Manifest** (signed; optional encrypted sections)
- **Commentary Work** (overlay or fork)
- **Merge Request** (proposed changes + review thread)
- **Policy Snapshot** (tool terms used for compatibility decisions)

### 11.2 Relationships
- Project has many Versions
- Version references Assets + Intent + Provenance + Rights
- Commentary Work targets a Project/Version and may reference Assets
- Fork creates a new Project linked to parent; merges flow through Merge Requests

---

## 12) Export and Interoperability

### 12.1 Export Modes
- **Flat export**: final media file + embedded public rights label where possible
- **Sidecar export**: media + provenance manifest (signed) + optional encrypted payload
- **DIYA Package export**: a single archive containing:
  - media assets
  - manifests
  - rights binder
  - intent card
  - signatures

### 12.2 Verification
DIY A should provide a “Verify Provenance” action that checks:
- asset hash matches manifest
- signatures validate
- policy snapshot referenced is present
- license compatibility decision was computed from stored rules

---

## 13) Design Principles for Implementation

### 13.1 Default to Creator Safety
- no blank-canvas generation in DIYA mode
- prompt commitment rather than forced public prompt disclosure
- license compatibility checks at publish time
- clear UI explanations when something is blocked

### 13.2 Default to Auditability
- immutable, signed manifests
- versioned intent and changelogs
- reproducible “what changed” history

### 13.3 Default to Community Trust
- visible authorship claims and process summaries
- remix permissions are explicit
- automatic credit propagation across forks/merges

---

## 14) Success Criteria (Conceptual)

DIYA is succeeding if:
- creators produce more distinctive, intent-aligned work than “generate-first” workflows
- accidental IP traps decline due to seed/intent gates and clean-room patterns
- professional users can export compliance/provenance packs confidently
- community norms reward process, critique, and craft (not mass output)
- collaboration scales via commentary + forks + merges without credit disputes

---

## 15) Open Questions for Future Versions
- How strict should DIYA be about “style mimicry” and branded character generation?
- What default cryptographic and key management UX best fits creators (simple vs power-user)?
- Should DIYA provide built-in similarity risk warnings (optional) before publishing?
- How should monetization (commissions/marketplace) interact with provenance requirements?

---

## Appendix A: DIYA “Clean-Room Character” Pattern (Conceptual)
To reduce accidental convergence to famous archetypes:
- require a structured design seed (silhouette, materials, role, gear, “avoid tropes” list)
- generate silhouettes first, details second
- ban naming living artists/brands/characters in prompts for “brandless original” categories
- require a human transformation step before Gallery publishing

---

## Appendix B: Minimal “Nutrition Label” Template (Public)
- AI used: (none / assist / generate / train)
- Tool/model: (name + version)
- Human seed: (present yes/no)
- Provenance: (verified yes/no)
- License: (id + link)
- Prompt: (open / committed / private)

---

End of DIYA Conceptual Specification v0.1
