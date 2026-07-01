# How to Use the KB Creation Framework (Modular)

**Version:** 2.0 | **Date:** 2026-04-09

---

## Creating a Knowledge Base from Scratch

### What You Need

- Access to 2-3 different AI model families (e.g., Claude, GPT, Gemini)
- An AI assistant with tool/file access for the Lead session
- Web search capability for fact-checking
- This directory and its contents

### Start Here: Launch the Orchestrator

The orchestrator (`prompts/01-orchestrator.md`) is your entry point. It turns the Lead model into a pipeline manager that tells you what to run, when, and with which prompt.

**Step 1:** Open a session with your Lead model. This should be a different model family than your two builders (e.g., if builders are Claude Sonnet and GPT-5.4, use Claude Opus or Gemini as Lead).

**Step 2:** Scope the project. Either:
- Run the Info Gather module (`02-info-gather.md`) to produce a scoping report, or
- Fill in the orchestrator's `[BRACKETED_VARIABLES]` manually if you already know the scope

The scoping report answers: what's the KB about, who's the audience, what topics are in/out, what sources matter, and what size are we targeting.

**Step 3:** Fill in the variables in `prompts/01-orchestrator.md`:

| Variable | What to fill in | Example |
|----------|----------------|---------|
| `[KB_NAME]` | Your domain | "Standards Essential Patents" |
| `[KB_TYPE]` | Category | "legal_topic" |
| `[AUDIENCE]` | Target readers | "Senior CELA practitioners" |
| `[USE_CASE]` | How the KB will be used | "Advisory — expert voice" |
| `[SIZE_TARGET]` | Per-file target | "18-30KB per file" |
| `[STATE_ROOT]` | Where to save output | Path to KB directory |
| `[model name]` (×3) | Builder A, Builder B, Lead | "Claude Sonnet 4.5", "GPT-5.4", "Claude Opus 4.6" |

**Step 4:** Paste the filled-in orchestrator prompt into the Lead's session. The Lead now manages the pipeline. It will tell you:
- Which prompt file to run next
- Which model to give it to
- What input to include
- When to stop and wait for your approval

**Step 5:** Follow the Lead's instructions. The pipeline runs:

```
Phase 1a   Research         Both builders propose topic structures
           ↓ Human gate — you approve the topic list
Phase 1b   Drafting         Each builder drafts one section at a time
Phase 1.5  Remediation      Lead triages; re-drafts thin sections if needed
Phase 2    Review           Lead reviews structure (2a), then depth (2b)
Phase 3    Revision         Each builder revises based on Lead's review
Phase 4    Comparison       Lead compares both v2 drafts
           ↓ Human gate — review comparison report
Phase 5    Fact-check       Currency (5a), citations (5b), classification (5c)
           5a/5b/5c can run in parallel
Phase 6    Assembly         Lead builds final KB from best of both
Phase 6a   Filename check   Verify all cross-reference filenames resolve
Phase 6b   Assembly check   Verify citations/claims added during assembly
Phase 6.5  Deepening        Expand any files below size gates
Phase 7    Audit            Quality rating per file, final human review
```

---

## Running It Manually (Without the Orchestrator)

If you prefer to sequence the prompts yourself, here's the step-by-step.

### 1. Scope the Project

Use `02-info-gather.md` to produce a scoping report — or write one yourself covering: topic, audience, scope boundaries, key sources, size targets. The Builder needs this as input.

### 2. Builder Research (both builders, separate sessions)

Open `prompts/02-builder-research.md`, fill in variables, paste into Builder A's session. Repeat in a separate session for Builder B.

**Output:** Each builder proposes a topic list.
**Human gate:** Review both lists. Approve, modify, or merge.

### 3. Builder Drafting (once per topic, per builder)

Open `prompts/03-builder-draft.md`. Fill in `[SECTION_NAME]` and `[SECTION_NUMBER]` for the first topic. Paste into Builder A's session. Save output. Repeat for every topic, then repeat the full sequence for Builder B.

This is the key advantage — each section gets the model's full attention instead of competing with 20 other sections in a single prompt.

### 4. Lead Review (once per builder)

Give `prompts/04-lead-review-structure.md` to the Lead with a builder's draft. Then give `prompts/05-lead-review-depth.md` with the same draft. Combine into one review document per builder.

### 5. Builder Revision (each builder, separately)

Give `prompts/06-builder-revision.md` to each builder with their draft + the Lead's combined review. Output: v2 drafts.

### 6. Lead Comparison

Give `prompts/07-lead-comparison.md` to the Lead with both v2 drafts.

### 7. Fact-Checking (can run in parallel)

Give the Lead these three prompts with the relevant claims:
- `prompts/08-factcheck-currency.md` — statutes still in force?
- `prompts/09-factcheck-citations.md` — citations exist and correct?
- `prompts/10-factcheck-classification.md` — categorizations correct?

Zero tolerance: unverifiable citations are removed, not flagged.

### 8. Assembly

Give `prompts/11-lead-assembly.md` to the Lead with both v2 drafts, comparison report, and all fact-check results. The assembly prompt now requires a **new claims manifest** — a list of all citations and factual claims the assembler adds that weren't in either builder draft.

### 9. Filename Validation

Run `prompts/12-filename-validation.md` on the assembled KB. This verifies every cross-reference link points to an actual file. Assembly agents consistently use intuitive shortnames instead of real filenames — this step catches that.

### 10. Post-Assembly Fact-Check

Run `prompts/13-post-assembly-factcheck.md` using the new claims manifest from assembly. This verifies citations and factual claims that were *added during assembly* and therefore bypassed Phase 5. This is the highest-impact quality gate — assembly typically generates 2-3x the builder content.

### 11. Final Audit

Invoke the Fact-Checker (`04-fact-checker.md`, Mode 2) on the assembled KB.

---

## Directory Structure

```
KB Creation - Modular/
├── 01-shared-axioms.md          Principles, error patterns, model profiles, thresholds
├── 02-info-gather.md            Scoping and requirements elicitation
├── 03-builder.md                Build pipeline rules and anti-patterns
├── 04-fact-checker.md           Independent verification protocol (5 modes)
├── 05-build-orchestrator.md     Pipeline management, decision logic, concurrency
├── 06-refresh-orchestrator.md   Ongoing maintenance and staleness detection
├── 07-audit-system.md           Institutional memory, Option C lesson promotion
├── HOW-TO-USE.md                This file
└── prompts/                     Copy-paste execution prompts
    ├── 00-README.md             Prompt inventory and sequencing
    ├── 01-orchestrator.md       Entry point — give to Lead first
    ├── 02-builder-research.md   Phase 1a: topic structure
    ├── 03-builder-draft.md      Phase 1b: one section at a time
    ├── 04-lead-review-structure.md  Phase 2a: structural review
    ├── 05-lead-review-depth.md      Phase 2b: depth review
    ├── 06-builder-revision.md       Phase 3: revise based on review
    ├── 07-lead-comparison.md        Phase 4: compare both drafts
    ├── 08-factcheck-currency.md     Phase 5a: statute currency
    ├── 09-factcheck-citations.md    Phase 5b: citation verification
    ├── 10-factcheck-classification.md  Phase 5c: classification check
    ├── 11-lead-assembly.md          Phase 6: final assembly
    ├── 12-filename-validation.md    Phase 6a: cross-reference check
    └── 13-post-assembly-factcheck.md Phase 6b: verify assembly-added claims
```

**Modules** (01-07) define the rules, architecture, and anti-patterns.
**Prompts** are what you actually paste into each model session.

---

## Model Selection

| Configuration | Builder A | Builder B | Lead | Notes |
|--------------|-----------|-----------|------|-------|
| **Standard** | GPT-5.4 | Goldeneye | Claude Opus 4.6 | Three families, maximum divergence |
| **Fallback** | Claude Sonnet 4.5 | GPT-5.4 | Claude Opus 4.6 | If Goldeneye unavailable; add self-scrutiny for Sonnet output |

**Model notes** (from `01-shared-axioms.md` Section 4):
- **GPT-5.4:** Precise, zero fabrication across 19 KBs. Builder A. Also best for deepening/fact-check.
- **Goldeneye:** Internal model, distinct family. Builder B.
- **Claude Opus 4.6:** Best judgment for Lead — comparison, synthesis, assembly.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Builder misses sections | Use per-topic drafting (`prompts/03-builder-draft.md`) |
| Thin closing sections | Per-topic drafting, or reorder so critical topics aren't last |
| Hallucinated citations | Phase 5b catches these — enforce zero tolerance |
| Builders produce similar output | Verify separate sessions; use different model families |
| Lead summarizes instead of synthesizing | Add explicit instruction: "assembled version must be AT LEAST as long as the longest builder version per section" |
| Timeout on large writes | Chunk: 3-4 sections at a time, save between |
| Context window exhausted | Break into new session; provide pipeline_state.md for resumption |

---

## The Four Rules (Apply to Every Prompt)

1. **Source-Constrained Specificity** — Never request detail without naming the source type
2. **Zero Tolerance for Fabrication** — Verified or removed. Never flagged, never hedged.
3. **Builder Isolation** — Builders never see each other's output
4. **No Self-Grading** — No model judges its own work
