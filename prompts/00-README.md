# Execution Prompts — KB Creation Framework (Modular)

**Version:** 1.0 | **Date:** 2026-04-09
**Parent modules:** 03-builder.md, 04-fact-checker.md, 05-build-orchestrator.md

## Purpose

These are the copy-paste execution prompts for each step of the KB build
pipeline. The parent modules (in the directory above) define the rules,
anti-patterns, and architecture. These prompts are what you actually feed
to each model session.

## Why Separate Prompts?

Models front-load attention on long prompts — instructions at the top get
more weight than instructions at the bottom. A single builder prompt that
asks a model to research, structure, and draft 20+ sections produces strong
openings and weak closings (GPT-5.4 missed 11 of 27 sections in a
monolithic build). Decomposing into one-task-per-prompt gives each step the
model's full attention.

## Prompt Inventory (Execution Order)

| # | File | Phase | Who | What |
|---|------|-------|-----|------|
| 01 | `01-orchestrator.md` | All | Lead | Entry point — manages the full pipeline |
| 02 | `02-builder-research.md` | 1a | Each builder | Research domain, propose topic structure |
| 03 | `03-builder-draft.md` | 1b | Each builder (per topic) | Draft one section at a time |
| 04 | `04-lead-review-structure.md` | 2a | Lead | Structural completeness review |
| 05 | `05-lead-review-depth.md` | 2b | Lead | Per-section depth and citation review |
| 06 | `06-builder-revision.md` | 3 | Each builder | Revise draft based on Lead's review |
| 07 | `07-lead-comparison.md` | 4 | Lead | Compare both revised drafts |
| 08 | `08-factcheck-currency.md` | 5a | Lead/checker | Verify statutes and cases are current |
| 09 | `09-factcheck-citations.md` | 5b | Lead/checker | Verify every citation exists |
| 10 | `10-factcheck-classification.md` | 5c | Lead/checker | Verify statutory tier mappings |
| 11 | `11-lead-assembly.md` | 6 | Lead | Assemble final KB from best of both |
| 12 | `12-filename-validation.md` | 6a | Lead/auto | Verify all cross-reference filenames |
| 13 | `13-post-assembly-factcheck.md` | 6b | Lead/checker | Verify claims added during assembly |

## Variables

All prompts use `[BRACKETED_VARIABLES]` populated from the scoping report
(produced by `02-info-gather.md`) or pipeline state.

| Variable | Source | Example |
|----------|--------|---------|
| `[KB_NAME]` | Scoping report | "Standards Essential Patents" |
| `[KB_TYPE]` | Scoping report | "legal_topic" |
| `[AUDIENCE]` | Scoping report | "Senior CELA practitioners" |
| `[USE_CASE]` | Scoping report | "Advisory — expert voice" |
| `[TOPICS_IN_SCOPE]` | Scoping report | Numbered topic list |
| `[TOPICS_OUT_OF_SCOPE]` | Scoping report | Explicit exclusions |
| `[KEY_SOURCES]` | Scoping report | "Court decisions, USPTO records" |
| `[SIZE_TARGET]` | Scoping report | "18-30KB per file" |
| `[SECTION_NAME]` | Per-topic | "US Federal Patent Policy" |
| `[SECTION_NUMBER]` | Per-topic | "03" |
| `[STATE_ROOT]` | Pipeline config | Path to KB directory |

## Sequencing

```
01-orchestrator.md          (give to Lead at session start)
    │
    ├─► 02-builder-research.md    (each builder, separate sessions)
    │   └─► Human approves topic structure
    │
    ├─► 03-builder-draft.md       (each builder, once per topic)
    │   └─► Lead triages — remediation if needed (reuse 03)
    │
    ├─► 04-lead-review-structure.md  (Lead, per builder)
    ├─► 05-lead-review-depth.md      (Lead, per builder)
    │   └─► Combined into one review per builder
    │
    ├─► 06-builder-revision.md    (each builder, with review)
    │
    ├─► 07-lead-comparison.md     (Lead, both v2 drafts)
    │
    ├─► 08-factcheck-currency.md       ┐
    ├─► 09-factcheck-citations.md      ├─ can run in parallel
    ├─► 10-factcheck-classification.md ┘
    │
    ├─► 11-lead-assembly.md       (Lead, final assembly + new claims manifest)
    │
    ├─► 12-filename-validation.md (Lead, verify all cross-ref filenames)
    │
    └─► 13-post-assembly-factcheck.md (Lead, verify claims added during assembly)
```

## Universal Principles (from 01-shared-axioms.md)

These four rules apply to EVERY prompt:

1. **Source-Constrained Specificity** — Never request detail without a source anchor
2. **Zero Tolerance for Fabrication** — Verified or removed, never flagged
3. **Builder Isolation** — Builders never see each other's output
4. **No Self-Grading** — No model judges its own work
