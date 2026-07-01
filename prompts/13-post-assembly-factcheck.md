# Post-Assembly Fact-Check (Phase 6b)

**Role:** Lead or dedicated fact-check agent
**Input:** Assembled KB files + new claims manifest (from Phase 6)
**Output:** Verification of all claims added during assembly
**Runs:** Once, after filename validation (Phase 6a)
**Why this exists:** Assembly typically generates 2-3x the content of the builder drafts. In the Trade/Export Controls test build, assembly produced 685KB from 193KB of builder drafts — meaning ~490KB (70%) was new content that bypassed the Phase 5 fact-check. This phase closes that gap.
**Reference:** 04-fact-checker.md for verification protocol

---

## Prompt

```
You are verifying the factual accuracy of content that was ADDED
during assembly of the [KB_NAME] knowledge base — content that was
NOT present in either builder draft and therefore was NOT checked
during Phase 5.

NEW CLAIMS MANIFEST: [paste from sources/new_claims_manifest.md]
  (If no manifest exists, you must extract new claims yourself by
  comparing assembled files against both builder drafts.)

BUILDER A DRAFT: [paste or attach — for comparison only]
BUILDER B DRAFT: [paste or attach — for comparison only]

TASK:

For each claim in the manifest (or each claim you identify as new):

1. CITATIONS added during assembly:
   - Does the cited source exist?
   - Is the citation correct (parties, reporter, year, section)?
   - Does the source say what the KB claims?
   - Apply the same zero-tolerance standard as Phase 5b.

2. FACTUAL CLAIMS added during assembly:
   - Dollar amounts, dates, percentages, thresholds
   - Entity names, agency names, program names
   - Statutory section numbers, CFR references
   - Verify each via web search against primary sources

3. ENFORCEMENT CASES added during assembly:
   - Does the case exist?
   - Are the parties, amounts, and dates correct?
   - Is the holding/outcome accurately described?

4. CLASSIFICATIONS added during assembly:
   - Statutory tier mappings
   - Regulatory categories
   - Entity type classifications
   - Verify against source text

VERIFICATION METHOD (per 04-fact-checker.md):
- Web search with specific, targeted queries
- Use primary sources: official statute text, court databases, CFR,
  agency press releases
- Source hierarchy: Tier 1 (primary) > Tier 2 (authoritative) > Tier 3
- Do NOT rely on training data
- Record the source URL for every verification

EXECUTION CONSTRAINT:
Do NOT delegate or decompose this verification into sub-tasks or
sub-agents. Execute all verifications yourself, sequentially, in this
session. Sub-agent decomposition risks inconsistent verification
standards and makes it harder to track provenance of each verdict.

ZERO TOLERANCE RULE:
If a new claim cannot be verified after reasonable search (3+ queries),
it must be REMOVED from the KB. Not flagged — removed.

EXCLUSION SAFEGUARD:
Before removing any claim, run at least 3 different search queries.
Log the queries. Only exclude if ALL searches return zero results.
(01-shared-axioms.md Pattern 5: Overcautious Exclusion)

OUTPUT FORMAT:
| # | Claim | File | Type | Verification | Source URL | Verdict |
|---|-------|------|------|-------------|-----------|---------|

Summary: [N] new claims checked, [N] confirmed, [N] corrected,
[N] removed

BLOCKING items (fabricated citations, wrong amounts, incorrect
classifications): [list with corrections]

Non-blocking items (minor date imprecision, format inconsistency):
[list]
```
