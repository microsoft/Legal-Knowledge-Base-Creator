# Audit System — KB Creation Framework
# Version: 1.0 | Created: 2026-04-09
# Scope: Per-KB verification logs, master audit log, cross-KB pattern detection, lesson promotion.
# Dependencies: Reads 01-shared-axioms.md for error patterns, thresholds, and existing lessons.
# Independence: Operates as a passive accumulator. Does not trigger builds or refreshes.
# Key mechanism: Option C — threshold-based lesson promotion with human confirmation.

---

## PURPOSE

This module maintains the institutional memory of the KB creation framework. It handles:

- **Per-KB audit trails** — every verification, correction, and decision recorded
- **Master audit log** — cross-KB findings aggregated for pattern detection
- **Option C lesson promotion** — when the same error pattern appears in 3+ KBs, auto-draft a lesson for Shared Axioms, pending human confirmation
- **Error pattern tracking** — identifying systemic issues vs. one-off mistakes

It does NOT verify content (that's the Fact-Checker). It does NOT fix KBs (that's the Builder or Refresh Orchestrator). It observes, records, detects patterns, and proposes process improvements.

---

## SECTION 1: PER-KB AUDIT TRAIL

### Location

Every KB maintains two files in `sources/`:

1. **`verification_log.md`** — append-only log of every fact-check and verification event
2. **`audit_trail.md`** — append-only log of every structural decision, correction, and status change

### Verification Log Format

```markdown
# Verification Log — [KB Name]
# Created: [date]
# Entries: [count]

---

## [Date] — [Event Type]
**Agent:** [model used]
**Mode:** [pipeline / post-ship / refresh / document / correction]
**Claims checked:** [N]
**Results:** [N confirmed] / [N corrected] / [N removed] / [N unverifiable]
**Error rate:** [%]
**Corrections applied:**
- [File]: [old claim] → [new claim] (source: [URL])
- [File]: [removed claim] (reason: [fabricated/stale/unverifiable])
**Patterns noted:** [any recurring issues]

---
```

### Audit Trail Format

```markdown
# Audit Trail — [KB Name]
# Created: [date]
# Entries: [count]

---

## [Date] — [Event Type]
**Action:** [built / rebuilt / refreshed / patched / deepened / audited]
**Pipeline version:** [v1.0 / v1.4 / v1.5 / modular]
**Models used:** [list]
**Key decisions:**
- [decision]: [rationale]
**Outcome:** [ship / re-verify / rebuild]
**Error rate:** [% if applicable]
**Files affected:** [list]

---
```

### What Gets Logged

| Event | Logged In | Required Fields |
|-------|-----------|----------------|
| Fact-check (any mode) | verification_log | Claims, results, corrections, error rate |
| Post-ship audit | Both | Full audit results + ship/rebuild decision |
| Refresh cycle | Both | Staleness findings + updates applied |
| Build decision | audit_trail | Rebuild vs. patch rationale |
| Correction applied | verification_log | Old → new with source |
| Deepening | audit_trail | Files deepened, sizes before/after |
| Error pattern detected | audit_trail | Pattern, count, KB list |

### Immutability

Both logs are **append-only**. Entries are never edited or deleted. If a prior entry was wrong, add a new entry correcting it with a reference to the original entry date.

---

## SECTION 2: MASTER AUDIT LOG

### Location

`C:\Users\davidrud\OneDrive - Microsoft\AI Projects\KB Creation Framework\master_audit_log.md`

This is a framework-level file. It aggregates findings across all KBs.

### Format

```markdown
# Master Audit Log — KB Creation Framework
# Last updated: [date]
# KBs tracked: [N]
# Total audits: [N]

---

## Portfolio Summary

| KB | Type | Last Audit | Error Rate | Status | Build Version |
|----|------|-----------|------------|--------|---------------|
| [name] | [company/legal/litigation] | [date] | [%] | [ship/re-verify/rebuild] | [v1.0/v1.4/v1.5/modular] |

## Error Rate Trends

| KB | Build | Post-Audit 1 | Rebuild | Post-Audit 2 |
|----|-------|-------------|---------|---------------|
| Adobe | 32% | — | 8% | — |
| MS Gaming Legal | 16% | — | 8% | — |

---

## Cross-KB Error Patterns

### Pattern: [Name]
- **Occurrences:** [N] KBs
- **KBs affected:** [list]
- **Description:** [what the pattern is]
- **Root cause:** [why it keeps happening]
- **Lesson status:** [not yet / drafted / promoted to Shared Axioms as Lesson N]

---

## Audit History (Reverse Chronological)

### [Date] — [KB Name] — [Event]
**Error rate:** [%]
**Key findings:** [brief]
**Corrections:** [N applied]
**Patterns detected:** [any]

---
```

### What Gets Aggregated

The master log pulls from per-KB logs:
- Error rates from every post-ship audit
- Correction counts and types
- Recurring error descriptions
- Build/rebuild decisions and rationale

### Update Frequency

Updated after every:
- Post-ship audit
- Refresh cycle that includes verification
- Rebuild completion
- Cross-KB pattern detection

---

## SECTION 3: OPTION C — THRESHOLD-BASED LESSON PROMOTION

### The Mechanism

When the same error pattern appears in 3 or more KBs, the Audit System:
1. **Detects** the pattern from per-KB verification logs
2. **Drafts** a candidate lesson for Shared Axioms
3. **Presents** it to the human for review
4. **Promotes** it to Shared Axioms upon human confirmation

### Why 3 KBs?

- 1 occurrence = noise (could be one-off)
- 2 occurrences = emerging signal (worth noting, not codifying)
- 3 occurrences = systemic pattern (warrants a process change)

This threshold balances sensitivity (catching real patterns) with specificity (not over-fitting to random errors).

### Pattern Detection

Compare error descriptions across per-KB verification logs. Patterns to watch for:

| Pattern Type | Detection Signal | Example |
|-------------|-----------------|---------|
| Fabrication cluster | Same type of claim fabricated in 3+ KBs | Fabricated settlement amounts |
| Stale data persistence | Same type of data found stale in 3+ KBs | FY projections surviving into next FY |
| Overcautious exclusion | Real facts removed as fabrication in 3+ KBs | Confirmed events flagged as implausible |
| Source rot | Same type of citation gone dead in 3+ KBs | Press release URLs returning 404 |
| Model-specific error | Same model makes same mistake in 3+ KBs | Sonnet inventing case citations |
| Structural deficiency | Same structural gap in 3+ KBs | Missing table of authorities |

### Lesson Draft Format

```markdown
## Candidate Lesson — [Draft Number]

**Pattern:** [Name]
**Occurrences:** [N KBs]
**Evidence:**
- [KB 1]: [specific instance]
- [KB 2]: [specific instance]
- [KB 3]: [specific instance]

**Proposed Lesson Text:**
> Lesson [N]: [Title]. [Body text — 2-4 sentences describing the pattern and the rule to prevent it.]

**Proposed Placement:** Shared Axioms Section [N], after Lesson [N]

**Status:** PENDING HUMAN REVIEW
```

### Promotion Workflow

```
Pattern detected in 3+ KBs
  │
  ▼
Audit System drafts candidate lesson
  │
  ▼
Human reviews draft
  │
  ├─► Approve → Lesson added to Shared Axioms, numbered, effective immediately
  ├─► Modify → Human edits text → added to Shared Axioms
  └─► Reject → Logged as "reviewed, not promoted" with rationale
```

### Current Lessons (Promoted via This Process)

Reference: Shared Axioms Section 7 (Lessons Learned). As of framework creation, 23 lessons exist from the v1.0-v1.5 lifecycle. Option C adds to this list going forward.

---

## SECTION 4: ERROR CLASSIFICATION

### Severity Levels

| Level | Definition | Impact | Example |
|-------|-----------|--------|---------|
| **Critical** | Factually wrong claim that could mislead legal advice | Immediate correction required | Wrong court ruling date, fabricated settlement amount |
| **Major** | Stale data or significantly inaccurate characterization | Correction before next use | Outdated market share, wrong executive title |
| **Minor** | Imprecise but not wrong, or cosmetic issues | Correct at next refresh | Rounded dollar amount, minor date discrepancy |
| **Cosmetic** | Formatting, style, or structural issues | No urgency | Missing YAML metadata, inconsistent header levels |

### Error Rate Calculation

Error rate = (Critical + Major errors) / total claims sampled × 100

Minor and cosmetic errors are tracked but do NOT count toward the error rate that determines ship/rebuild decisions.

---

## SECTION 5: PORTFOLIO HEALTH METRICS

### Tracked Metrics

| Metric | Definition | Healthy Range |
|--------|-----------|---------------|
| **Portfolio error rate** | Average error rate across all KBs | < 8% for legal, < 12% for company |
| **Freshness score** | % of KBs within their refresh interval | > 80% |
| **Size compliance** | % of KBs passing all size gates | > 90% |
| **Lesson velocity** | New lessons promoted per quarter | 1-3 (more suggests systemic issues) |
| **Rebuild rate** | % of KBs requiring full rebuild at audit | < 10% after initial build cycle |
| **Correction density** | Average corrections per audit | Declining over time |

### Trend Tracking

After each audit cycle, update the master log with:
- Current metric values
- Comparison to prior cycle
- Direction of trend (improving / stable / degrading)

A degrading trend in any metric should be flagged for human review.

---

## SECTION 6: ANTI-PATTERNS

### Don't edit historical entries
The logs are append-only. If entry #5 was wrong, don't change it — add entry #12 that says "Correction to entry #5: [explanation]."

### Don't promote lessons prematurely
Two occurrences is a signal, not a pattern. Wait for the third. Premature lessons create rules that don't generalize.

### Don't conflate error types
A fabricated claim and a stale data point are both errors, but they have different root causes and different fixes. Track them separately.

### Don't ignore the denominator
An error rate of 20% on 5 claims (1 wrong) is very different from 20% on 100 claims (20 wrong). Always report both the rate and the sample size.

### Don't auto-promote lessons
Option C drafts lessons automatically but NEVER promotes them without human confirmation. The human confirms because: (a) they may see context the system doesn't, (b) the lesson text may need refinement, (c) not every pattern deserves a permanent rule.

---

## SECTION 7: BOOTSTRAPPING

### For Existing KBs (Pre-Modular)

KBs built before the modular framework won't have verification_log.md or audit_trail.md. Bootstrap them:

1. Create `sources/verification_log.md` with a header entry:
   ```
   ## [Date] — Bootstrap
   **Note:** This KB was built under v[X] of the monolithic guide.
   **Prior audit reports:** [list any existing audit reports in sources/]
   **Imported error rate:** [from most recent audit]
   ```

2. Create `sources/audit_trail.md` with build history reconstructed from available artifacts.

3. Import existing audit data into the master audit log.

### For the Master Audit Log

Initialize with the current portfolio data:
- 19 company KBs with v1.4 audit results
- 15 topic KBs with v1.4 audit results
- Rebuild results (Adobe, Gaming Law, MS Gaming Legal, Recent Meta Cases)

---

*This module is the framework's memory. It records what happened, detects what keeps happening, and proposes changes to prevent it from happening again. It never acts — it observes, accumulates, and recommends.*
