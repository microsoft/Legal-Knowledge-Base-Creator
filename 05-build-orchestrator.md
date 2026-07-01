# Build Orchestrator — KB Creation Framework
# Version: 1.0 | Created: 2026-04-09
# Scope: Manages the build pipeline for new or rebuilt KBs. Project-based — starts and ends.
# Dependencies: Reads 01-shared-axioms.md. Invokes 02-info-gather, 03-builder, 04-fact-checker.
# Independence: Can operate as a standalone runbook for anyone picking up a build mid-stream.

---

## PURPOSE

This module manages the end-to-end KB creation pipeline. It handles:

- Pipeline sequencing and handoff between modules
- Decision logic (rebuild vs. patch, which builder is backbone, ship vs. reject)
- Concurrency management
- Failure handling and retries
- Progress tracking
- Session handoff (so a fresh agent can pick up mid-pipeline)

It is the only module that knows about all the others. The other modules are self-contained.

---

## SECTION 1: PIPELINE SEQUENCE

### Standard Build (New KB)

```
Step 1: INFO GATHER
  → Input: User request
  → Output: sources/scoping_report.md
  → Decision: Is scope clear? If no, ask user.

Step 2: BUILDER — Phase 1 (Dual Builders)
  → Input: scoping_report.md + shared-axioms.md
  → Output: sources/kb_[model_a].md, sources/kb_[model_b].md
  → Launch: Both builders in parallel
  → Wait: Both complete

Step 3: BUILDER — Phase 2 (Lead Review)
  → Input: Both builder drafts
  → Output: comparison_report.md, review_[a].md, review_[b].md
  → Launch: Single Lead agent
  → Wait: Complete

Step 4: BUILDER — Phase 3 (Revision)
  → Input: Revision instructions + other builder's draft
  → Output: sources/kb_[model_a]_v2.md, sources/kb_[model_b]_v2.md
  → Launch: Both builders in parallel
  → Wait: Both complete

Step 5: BUILDER — Phase 4+5 (Assembly)
  → Input: Revised drafts + comparison report + fact-check corrections
  → Output: Assembled KB files + build_manifest.md
  → Launch: Single assembly agent
  → Decision: Run size gate. If fail → Phase 5.5 (deepening). If pass → continue.

Step 6: FACT-CHECKER — Full Verification
  → Input: Assembled KB files
  → Output: factcheck_report.md + verification_log entries
  → Launch: Single agent (GPT-5.4)
  → Decision: Apply corrections to KB files

Step 7: FACT-CHECKER — Post-Ship Audit
  → Input: Corrected KB files
  → Output: v14_audit_report.md
  → Launch: Single agent (GPT-5.4)
  → DECISION GATE: Check error rate against thresholds (Shared Axioms Section 5)
    → ≤ threshold: SHIP
    → > threshold: REBUILD (return to Step 2 with lessons learned)
```

### Rebuild (Existing KB, Failed Audit)

Same as Standard Build, except:
- Step 1 uses the existing KB's index.md and AGENTS.md as scoping input (still verify scope — don't build blind)
- Builders receive the existing KB files as reference (but must generate fresh drafts, not patch)
- The audit report from the failed prior version informs the scoping report's "known controversies" section

### Patch (Existing KB, Minor Corrections)

```
Step 1: Apply corrections from audit report directly to KB files
Step 2: FACT-CHECKER — Correction Verification (Mode 5)
  → Verify only the corrected claims
  → If new errors introduced: escalate to Rebuild
  → If clean: SHIP
```

---

## SECTION 2: DECISION LOGIC

### Rebuild vs. Patch vs. Ship

| Signal | Action |
|--------|--------|
| Error rate ≤ ship threshold | **Ship** with corrections |
| Error rate > ship but ≤ rebuild threshold | **Patch** + re-audit |
| Error rate > rebuild threshold | **Full rebuild** |
| Size gate failure (any file < 12KB) | **Deepen** then re-assess |
| Structural issues (missing index, no AGENTS.md) | **Patch** — add missing artifacts |
| Scope mismatch (KB covers wrong topics) | **Rebuild** with new scoping report |

### Which Builder Is Backbone?

The Lead's comparison report recommends the backbone. Default heuristics:
- **GPT-5.4 (Builder A) is backbone** in >90% of cases — better sourcing, fewer fabrications
- **Goldeneye (Builder B) is backbone** only when: (a) it has significantly more coverage, AND (b) its factual accuracy is comparable
- When in doubt, use Builder A. The cost of enriching A with B's content is lower than de-fabricating B.

### Ship Decision

The Orchestrator does NOT make the ship decision unilaterally. The decision flow:
1. Fact-Checker produces audit report with error rate
2. Orchestrator checks against thresholds (Shared Axioms Section 5)
3. If within threshold: Orchestrator recommends SHIP
4. Human confirms (for legal KBs) or auto-ships (for lower-risk KBs if configured)

---

## SECTION 3: CONCURRENCY RULES

| Phase | Max Concurrent Agents | Rationale |
|-------|----------------------|-----------|
| Phase 1 (Builders) | 2 per KB (the two builders) | High context per agent |
| Phase 2 (Lead Review) | 1 per KB | Full-context read of both drafts |
| Phase 3 (Revision) | 2 per KB | Medium context |
| Phase 4+5 (Assembly) | 1 per KB | Highest context — merging everything |
| Phase 5.5 (Deepening) | 4-6 files in parallel | Low context per file |
| Fact-Check | 1 per KB | Full KB read |
| Post-Ship Audit | 1 per KB | Full KB read |

### Multi-KB Batch Rules

When building multiple KBs simultaneously:
- **Max 6 total agents** across all KBs (validated: 20+ causes persistent rate limiting)
- **Stagger pipeline phases** — don't launch 6 Lead Reviews simultaneously
- **Prefer: 2-3 KBs in Phase 1 while 1 KB in Phase 4+5** — mix high-concurrent with low-concurrent phases
- **Monitor rate limits** — if web search starts throttling, reduce concurrency

### Failure Handling

| Failure | Action |
|---------|--------|
| Agent timeout (>30 min) | Relaunch with same prompt |
| File write error (OneDrive sync) | Retry — usually transient |
| Rate limit on web search | Agent self-heals with backoff. If persistent, reduce concurrency |
| Agent produces empty/truncated output | Relaunch. If 2nd failure, switch models |
| Path error (directory moved) | Verify paths, relaunch with corrected paths |

---

## SECTION 4: PROGRESS TRACKING

### Pipeline State File

Maintain `sources/pipeline_state.md` in each KB being built:

```markdown
# Pipeline State — [KB Name]
# Last updated: [timestamp]

## Current Phase: [phase number and name]
## Status: [running / waiting / complete / blocked]

## Phase Log
| Phase | Started | Completed | Agent | Model | Notes |
|-------|---------|-----------|-------|-------|-------|
| 1A | [time] | [time] | [id] | Sonnet 4.5 | 89KB draft |
| 1B | [time] | [time] | [id] | GPT-5.4 | 102KB draft |
| 2  | [time] | [time] | [id] | Opus 4.6 | B backbone, 5 disputes resolved |
| 3  | [time] | [pending] | [id] | Both | Revising... |

## Decisions Made
| Decision | Rationale |
|----------|-----------|
| Builder B backbone | Lead scored B at 4.7 vs A at 3.4 |
| No deepening needed | All files >18KB, avg 23KB |

## Pending
- [ ] Phase 3 revision (both builders)
- [ ] Phase 4+5 assembly
- [ ] Fact-check
- [ ] Post-ship audit

## Blocked
[None / description of blocking issue]
```

### SQL Tracking (for multi-KB orchestration)

When managing multiple KBs, use SQL for queryable state:

```sql
CREATE TABLE kb_pipeline (
  kb_name TEXT PRIMARY KEY,
  kb_type TEXT,           -- company / legal_topic / litigation
  current_phase TEXT,
  status TEXT,            -- running / complete / blocked / ship / rebuild
  error_rate REAL,
  builder_a_size REAL,
  builder_b_size REAL,
  assembled_size REAL,
  started_at TEXT,
  completed_at TEXT
);
```

---

## SECTION 5: SESSION HANDOFF

When a session ends or a new agent picks up a build:

### What the New Agent Needs

1. **This module** (for pipeline logic)
2. **Shared Axioms** (for principles and standards)
3. **The KB's `sources/pipeline_state.md`** (for current progress)
4. **The KB directory** (to read existing artifacts)

### Handoff Checklist

Before ending a session with active builds:
- [ ] Update pipeline_state.md for every active KB
- [ ] Note all running agent IDs and what they're doing
- [ ] Record any decisions made and their rationale
- [ ] List what the next agent should do first

### Resumption Protocol

When picking up a build mid-stream:
1. Read pipeline_state.md
2. Check which artifacts exist in sources/
3. Determine current phase from artifact inventory:
   - Builder drafts exist but no comparison report → Phase 2 needed
   - Comparison + revised drafts exist but no assembled files → Phase 4+5 needed
   - Assembled files exist but no audit report → Fact-check needed
4. Continue from the current phase

---

## SECTION 6: COST ESTIMATION

### Per-KB Cost (Multi-Model Pipeline)

| Phase | Model | Estimated Cost |
|-------|-------|---------------|
| Phase 1A | Sonnet 4.5 | $3-5 |
| Phase 1B | GPT-5.4 | $2-4 |
| Phase 2 | Opus 4.6 | $3-5 |
| Phase 3 | Both | $2-4 |
| Phase 4+5 | GPT-5.4 or Opus | $2-4 |
| Deepening (if needed) | GPT-5.4 | $0.70/file |
| Fact-Check | GPT-5.4 | $1-2 |
| Post-Ship Audit | GPT-5.4 | $0.50-1 |
| **Total** | | **~$14-25** |

### Batch Cost

For N KBs: ~$17 × N (median). A 19-KB portfolio: ~$325. With deepening: ~$565.

---

*This module orchestrates KB builds. It knows the pipeline, manages concurrency, tracks progress, and makes threshold-based decisions. It invokes Info Gather, Builder, and Fact-Checker but does not do their work.*
