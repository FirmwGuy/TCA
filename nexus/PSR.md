# PSR v0.1 — PRISM Surface Realizer Specification
**Status:** Draft for implementation  
**Last updated:** 2025-12-26  
**Scope:** Deterministic, non-AI rendering of PRISM/NEXUS semantic content into human-readable pages (dictionary/encyclopedia/news) using a template+grammar library.

---

## 0. Purpose

PSR (PRISM Surface Realizer) is a **deterministic surface realization engine** that turns a **pre-selected, policy-governed set of semantic statements** (Claims, statuses, evidence summaries) into readable text (Markdown/HTML/plaintext) using a **template database** “in the spirit of ABNF.”

PSR is designed to be a “language filter,” not a thinker:

- It **does not decide what to include** (selection is done upstream, e.g., by NEXUS).
- It **does not infer importance** (tiers are supplied upstream).
- It **does not form opinions** (it cannot introduce new factual assertions or evaluative framing).
- It **does not fetch** (it operates only on the provided input bundle and template library).

PSR is suitable as:
- a default renderer,
- a safe fallback when generative AI is disallowed,
- a reproducible “deterministic view” for auditing.

---

## 1. Non-goals

PSR MUST NOT:
- determine truth,
- decide which claims are “important,”
- merge contradictions into a single story,
- produce new factual claims not explicitly supported by the input bundle,
- browse, retrieve, or call external tools.

PSR MAY:
- re-order items *within a pre-specified section ordering rule* (only if requested in the bundle),
- do deterministic formatting (dates, numbers, units),
- choose among templates via deterministic matching.

---

## 2. Position in the stack

PSR is typically a component of **NEXUS** (execution layer).

- **PRISM** provides canonical artifacts (Claims, Evidence chains, Packs).
- **WEAVE** provides governance receipts/outcomes (lane/status).
- **NEXUS** selects data under a Lens and produces a **Projection Plan**.
- **PSR** renders the plan into readable outputs.

> PSR MUST be treated as producing **derived views**, never canonical truth.

---

## 3. High-level architecture

### 3.1 Data flow
1) **NEXUS Projection Builder** constructs a `RenderBundle`:
   - the page kind (meaning/entity/event/digest),
   - the selected items (claims, disputes, evidence summaries),
   - importance tiers and section grouping,
   - the human-friendly labels to use (instance names, predicate labels),
   - a citation map (claim hashes).
2) PSR loads a `TemplateLibrary` (versioned).
3) PSR matches templates for each item and realizes:
   - section headers,
   - clause sentences,
   - evidence callouts,
   - dispute warnings,
   - truncation markers (“show more”).
4) PSR emits `RenderedPage` with:
   - content blocks,
   - citations,
   - provenance describing the template library version and rendering options.

### 3.2 Determinism contract
Given:
- the same `RenderBundle` bytes,
- the same `TemplateLibrary` bytes,
- the same PSR version,
PSR MUST produce **byte-identical output** for audit-grade render targets.

Normative v0.1 audit-grade targets:
- `RenderedPage.blocks` (when emitted): MUST be identical as an ordered block list.
- `RenderedPage.content` when `format` is `markdown` or `plaintext`: MUST be byte-identical.

HTML output (`format == "html"`) is OPTIONAL in v0.1; implementations MUST NOT rely on host-specific locale/timezone formatting or non-canonical HTML serializers in any path they claim is audit-grade.

---

## 4. Core concepts

### 4.1 RenderBundle
A `RenderBundle` is the sole input to PSR. It is produced upstream (e.g., by NEXUS).

It MUST contain everything needed for rendering:
- the items to render (claims and other blocks),
- labels to display for referenced nodes/meanings,
- policies already applied (at least as provenance hashes),
- ordering and importance tiers.

PSR MUST NOT reach outside the bundle.

### 4.2 TemplateLibrary
A `TemplateLibrary` is a versioned set of:
- **macro templates** (page recipes and section layouts),
- **micro templates** (claim clause patterns),
- **component templates** (evidence/dispute/truncation blocks),
- i18n variants (by language tag).

A TemplateLibrary is content-addressable and ideally publishable as an artifact.

### 4.3 Importance tiers
Importance tiers are integers (recommended 0–3) computed upstream:
- Tier 0: table/list only
- Tier 1: single sentence
- Tier 2: short paragraph
- Tier 3: focal paragraph + evidence/dispute callouts

PSR MUST only *consume* tiers; it MUST NOT compute them.

### 4.4 “No-opinion” guarantee (mechanical)
PSR achieves neutrality by construction:
- Output tokens come only from:
  - template literals, and
  - the provided labels/literals in the bundle.
- PSR MUST NOT introduce new nouns/verbs/adjectives beyond templates and provided values.
- Therefore “opinionated framing” is controlled by the TemplateLibrary, which is inspectable and governable.

---

## 5. RenderBundle format (normative)

### 5.1 Envelope
A RenderBundle is a JSON object:

```json
{
  "bundle_version": "0.1",
  "bundle_id": "sha256:...",
  "generated_at": "2025-12-26T00:00:00Z",

	  "lens": {
	    "lens_id": "sha256:...",
	    "contexts": [{ "context_id": "weave:context:...", "declaration_hash": "sha256:..." }],
	    "policies": {
	      "safety_policy": "sha256:...",
	      "ranking_policy": "sha256:...",
	      "quality_policy": "sha256:...",
	      "admission_policy": "sha256:...",
	      "service_policy": "sha256:...",
	      "index_policy": "sha256:...",
	      "projection_policy": "sha256:...",
	      "ingestion_policy": "sha256:..."
	    },
	    "editions": ["sha256:..."]
	  },

  "page": {
    "page_kind": "meaning | entity | event | digest",
    "title": { "text": "Paris", "lang": "en" },
    "subtitle": { "text": "City in France", "lang": "en" },
    "target": { "instance_id": "inst:...", "meaning_ref": null }
  },

  "i18n": {
    "language": "en",
    "fallback_languages": ["en"],
    "number_locale": "en-US",
    "date_locale": "en-US"
  },

  "labels": {
    "instances": {
      "inst:uuid:...": { "text": "Paris", "lang": "en" }
    },
    "meanings": {
      "meaningref:sha256:PACK...#uid:pred:located_in": { "text": "located in", "lang": "en" }
    },
    "predicates": {
      "meaningref:...#uid:pred:located_in": { "text": "located in", "lang": "en" }
    }
  },

  "sections": [
    {
      "section_id": "overview",
      "title": { "text": "Overview", "lang": "en" },
      "ordering": { "mode": "given" },
      "items": [
        { "item_id": "c1", "kind": "claim", "claim_hash": "sha256:..." }
      ],
      "budgets": { "max_items": 12, "truncated": false }
    }
  ],

  "claims": [
    {
      "claim_hash": "sha256:...",
      "importance_tier": 2,

      "subject": { "instance_id": "inst:uuid:..." },
      "predicate": { "meaning_ref": { "pack_hash": "sha256:...", "entry_uid": "uid:pred:located_in" } },
      "object": { "instance_id": "inst:uuid:..." },

      "polarity": "affirm | deny",
      "tense": "present | past | timeless",

      "qualifiers": [
        { "label": "as of", "value": { "literal": { "datatype": "date", "value": "2025-12-26" } } }
      ],

      "status": {
        "lane": "admitted | featured | proposed | suppressed | draft",
        "attestation": "attested | contested | none",
        "receipt_refs": ["sha256:..."]
      },

      "verifiability": {
        "pvl_declared": 2,
        "pvl_validated": "pass | fail | unknown",
        "pvl_failures": []
      },

      "evidence_summary": {
        "chains": [
          { "chain_id": "e1", "quality": "high | medium | low", "selector_precision": "high | medium | low" }
        ]
      },

      "render_hints": {
        "render_class": "location",
        "preferred_template_id": null
      }
    }
  ],

  "blocks": [
    {
      "block_id": "disputes",
      "kind": "dispute_block",
      "importance_tier": 2,
      "data": { "targets": ["sha256:..."], "summary": { "text": "Contested in this context.", "lang": "en" } }
    }
  ],

  "citations": {
    "style": "footnote | inline | endnote",
    "map": {
      "sha256:...": { "display": "[1]", "url": null }
    }
  },

  "constraints": {
    "neutral_register": true,
    "forbid_recommendations": true,
    "forbid_moral_language": true,
    "max_sentence_length": 240
  }
}
```

### 5.2 Required fields
PSR MUST require at least:
- `bundle_version`
- `page.page_kind`
- `i18n.language`
- `sections[]` (at least one)
- `claims[]` (may be empty for some meaning pages)
- `labels` for any instance/meaning referenced by items OR PSR must apply fallback labeling rules (§8.3)

### 5.3 RenderBundle identity
`bundle_id` SHOULD be the hash of the canonical bundle bytes (outside PSR scope, but recommended for replay/audit).

---

## 6. TemplateLibrary format (normative)

### 6.1 TemplateLibrary envelope
A TemplateLibrary is a JSON object:

```json
{
  "library_version": "0.1",
  "library_id": "sha256:...",

  "languages": ["en"],

  "globals": {
    "citation_style_default": "footnote",
    "neutral_register": true
  },

  "macros": [ /* page recipes */ ],
  "micro": [ /* claim clause templates */ ],
  "components": [ /* evidence, dispute, truncation, tables */ ],

  "forbidden_lexemes": {
    "en": ["obviously", "clearly", "should", "must", "evil", "insane"]
  }
}
```

### 6.2 Macro templates (page recipes)
Macro templates specify *how to render sections*.

Example:

```json
{
  "template_id": "macro.entity_page.en.v1",
  "kind": "macro",
  "match": { "page_kind": "entity", "lang": "en" },
  "render": {
    "layout": "sections",
    "section_order": ["overview","timeline","relations","evidence","disputes"],
    "section_header_pattern": "## {SECTION_TITLE}\n"
  }
}
```

Macro templates MUST NOT change the section membership (which items belong where). They only render the provided plan.

### 6.3 Micro templates (claim clause patterns)
Micro templates map claim types to sentence/paragraph patterns.

Example:

```json
{
  "template_id": "micro.location.en.v1",
  "kind": "micro",
  "match": {
    "lang": "en",
    "render_class": "location",
    "object_form": "instance",
    "polarity": "affirm",
    "tier": [1,2,3]
  },
  "render": {
    "pattern": "{SUBJECT} is located in {OBJECT}{TIME}{STATUS}.",
    "slots": {
      "TIME": { "when": "has_as_of", "pattern": " (as of {AS_OF_DATE})" },
      "STATUS": { "when": "contested", "pattern": " (contested)" }
    }
  }
}
```

### 6.4 Component templates
Components render common blocks such as:
- evidence callouts,
- dispute summaries,
- truncation (“show more”),
- tables (relations list),
- disclaimers.

Example dispute component:

```json
{
  "template_id": "comp.dispute.en.v1",
  "kind": "component",
  "match": { "lang": "en", "block_kind": "dispute_block" },
  "render": { "pattern": "> ⚠️ {SUMMARY} {CITATIONS}\n" }
}
```

---

## 7. Template language (PTL-0.1) (normative)

PSR uses a minimal template language that is deterministic and safe.

### 7.1 Placeholders
Placeholders are written `{NAME}` and replaced with string fragments.

Minimum required placeholders for micro templates:
- `{SUBJECT}`
- `{PREDICATE}` (optional if the verb phrase is encoded directly)
- `{OBJECT}`
- `{STATUS}` (often slot-based)
- `{CITATIONS}` (if citations are rendered inline)

Additional optional placeholders:
- `{AS_OF_DATE}`
- `{VALUE}` (for literal objects)
- `{UNIT}` (if qualifiers provide units)
- `{CONTEXT_NAME}` (if supplied in the bundle)

### 7.2 Slots (conditional subpatterns)
Slots allow optional segments under conditions:

```json
"slots": {
  "TIME": { "when": "has_as_of", "pattern": " (as of {AS_OF_DATE})" }
}
```

Slot `when` conditions are evaluated solely against the claim object:
- `has_as_of`, `has_valid_time`
- `contested`, `attested`
- `has_qualifier:<name>`
- `tier_at_least:<n>`

Unknown conditions MUST evaluate to false.

### 7.3 Escaping and safety
- PSR MUST HTML-escape values when rendering HTML.
- For Markdown output, PSR SHOULD escape characters that would change semantics (e.g., `[` `]` in link text), subject to a minimal safe escaping rule set.

### 7.4 Function tokens (optional)
PTL may optionally support pure formatting functions with no external state:
- `format_date(x, locale)`
- `format_number(x, locale)`
- `truncate(x, n)`
- `join(list, sep)`

Functions MUST be deterministic and side-effect free.

Formatting functions MUST NOT depend on host locale/timezone libraries. If `locale` is provided, it MAY be used only to select between **explicitly specified** deterministic formats; unknown locales MUST fall back to the deterministic defaults in §8.4.

---

## 8. Rendering algorithm (normative)

### 8.1 Overview (deterministic steps)
Given a RenderBundle `B` and TemplateLibrary `L`:

1) Choose macro template:
   - match on `page_kind` + `lang`
   - if none, use default macro for `sections` layout.

2) For each section in macro `section_order` (or bundle order if `given`):
   - render section header
   - for each item in section.items (in provided order unless specified otherwise):
     - if item.kind == claim:
       - render claim via micro template matching
     - else:
       - render via component template matching

3) Attach citations as required by bundle/constraints.

4) Apply forbidden lexeme check (optional but recommended):
   - output MUST NOT contain forbidden lexemes unless they appear in labels/literals (configurable).
   - if violation occurs, PSR MUST either:
     - (a) remove/replace via deterministic rule, or
     - (b) fail rendering and emit a deterministic fallback view (e.g., table form).

5) Emit RenderedPage.

### 8.2 Template matching precedence
Template matching MUST be deterministic.

Recommended precedence:
1) `preferred_template_id` if present and found
2) exact match on `template_id` referenced by claim render_hints
3) match on `render_class` + `lang` + `object_form` + `polarity` + `tier`
4) match on `predicate meaning_ref` (if provided as a match dimension)
5) fallback generic triple template:
   - `"{SUBJECT} — {PREDICATE_LABEL} — {OBJECT}."`

If multiple templates match at the same precedence level:
- choose the lexicographically smallest `template_id`.

### 8.3 Label fallback rules
If a referenced label is missing:
- Instances: display a shortened form of `instance_id` (e.g., last 8 chars) prefixed with `inst:` marker.
- Meanings: display `entry_uid` or `pack_hash#entry_uid`.
- Predicates: display `entry_uid` if label missing.

PSR MUST NOT call external resolvers to fill missing labels.

### 8.4 Object form rendering
Object forms:
- instance: use instance label
- meaning: use meaning label
- literal: format via datatype rules

Literal datatype minimum formatting:
- `string`: as-is (escaped as needed)
- `number`: canonical decimal string (no thousands separators)
- `date`: ISO date string as provided (recommended `YYYY-MM-DD`)
- `datetime`: ISO datetime string as provided (recommended UTC `...Z`)
- `url`: display as URL (optionally link)
- `hash`: display short hash

### 8.5 Status markers
Status markers are rendered only from bundle-provided status fields:
- lane and attestation.
PSR MUST NOT infer status.

Recommended rendering:
- contested: “(contested)” marker + optional link to receipts if URLs provided
- suppressed: do not render claim in default view; render placeholder block if requested (“suppressed in this context”)

---

## 9. Output format (normative)

### 9.1 RenderedPage schema
```json
{
  "rendered_version": "0.1",
  "format": "markdown | html | plaintext",
  "language": "en",

  "content": "…",          // main bytes for the chosen format
  "blocks": [              // structured representation (optional but recommended)
    { "block_kind": "section_header", "text": "Overview", "level": 2 },
    { "block_kind": "sentence", "text": "Paris is located in France.", "citations": ["sha256:..."] }
  ],

  "citations": {
    "style": "footnote",
    "entries": [
      { "id": "1", "claim_hash": "sha256:...", "label": "[1]" }
    ]
  },

  "provenance": {
    "bundle_id": "sha256:...",
    "template_library_id": "sha256:...",
    "psr_version": "0.1",
    "options": { "neutral_register": true }
  }
}
```

### 9.2 Canonicalization for output bytes
PSR MUST define canonical byte output rules per format:

- Markdown: `\n` newlines, no trailing spaces, final newline.
- Plaintext: `\n` newlines, final newline.
- HTML: serialized with stable attribute ordering and `\n` newlines (or emit `blocks` and let a canonical HTML serializer run elsewhere).

---

## 10. Neutrality and “no opinions” (normative)

### 10.1 Neutral register
If `constraints.neutral_register == true`, PSR MUST:
- avoid inserting evaluative words (the only inserted words are template literals),
- render disputed states explicitly (e.g., “contested” markers),
- avoid imperative language unless it exists in supplied labels/literals (which should be rare).

### 10.2 Forbidden recommendations
If `constraints.forbid_recommendations == true`, TemplateLibraries for that context MUST NOT contain recommendation verbs or imperatives.

Additionally, PSR SHOULD enforce a forbidden lexeme list and fail closed if violated.

### 10.3 No new factual assertions
PSR MUST NOT create new propositional content beyond:
- the subject/predicate/object of the claims,
- the qualifiers explicitly provided,
- the explicit status markers and provenance.

---

## 11. Internationalization (i18n)

### 11.1 Language packs
TemplateLibrary entries MUST be language-tagged.

PSR SHOULD support BCP-47 tags (e.g., `en`, `pt-BR`, `fr`).

Locale-specific formatting (dates/numbers) SHOULD be handled upstream (e.g., by supplying localized labels/literals in the RenderBundle). PSR’s built-in formatting MUST remain deterministic per §8.4.

### 11.2 Avoid runtime machine translation
PSR MUST NOT silently translate via online services. Any translation is:
- another derived artifact,
- governed by policy,
- and should be explicit.

---

## 12. Extensibility

PSR may be extended by:
- adding new render classes (`render_class`),
- adding new component kinds (evidence blocks, timelines),
- adding new placeholder functions (must remain deterministic),
- adding richer morphological support per language.

Any extension MUST preserve determinism:
- unknown extension features MUST fail closed or fall back to generic templates.

---

## 13. Conformance checklist

A PSR implementation is conformant to v0.1 if it:
1) Accepts a RenderBundle and TemplateLibrary and produces RenderedPage.  
2) Does not fetch data outside the bundle.  
3) Implements deterministic template matching with stable tie-breakers.  
4) Implements fallback rendering for unknown claim types.  
5) Supports at least one output format (Markdown RECOMMENDED).  
6) Preserves neutrality constraints when enabled.  
7) Produces stable output bytes given identical inputs and templates.  

---

## Appendix A — Recommended render classes (informative)

Common `render_class` values:
- `identity` (names, aliases)
- `type_of` (is_a / instance_of)
- `location` (located_in, part_of_region)
- `part_whole` (has_part, member_of)
- `authorship` (authored_by, created_by)
- `time_event` (occurred_at, founded_at)
- `measurement` (has_value, has_unit)
- `reference` (published_in, cited_by)
- `dispute` (contested, retracted, superseded)

---

## Appendix B — Example generic fallback templates (informative)

Generic micro template (English):
- Tier 1: `"{SUBJECT} — {PREDICATE_LABEL} — {OBJECT}.{STATUS} {CITATIONS}"`
- Tier 2+: `"{SUBJECT} {PREDICATE_LABEL} {OBJECT}.{STATUS} {CITATIONS}"`

---

## Appendix C — Why “Surface Realizer” is the right name (informative)

In natural language generation literature, a “surface realizer” is the module that:
- takes an abstract meaning representation,
- realizes it into grammatical surface text.

PSR is intentionally restricted to this role, making it appropriate for safety- and audit-critical knowledgebases.
