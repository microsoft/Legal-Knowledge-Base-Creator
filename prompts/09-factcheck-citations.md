# Fact-Check: Citation Verification (Decomposed — Phase 5b)

**Role:** Lead or dedicated fact-check agent
**Input:** All citations from v2 drafts
**Output:** Citation-by-citation verification
**Runs:** Once per build; can run in parallel with 5a and 5c
**Why decomposed:** Systematic per-citation verification; prevents batch fatigue
**Reference:** 04-fact-checker.md for full verification protocol

---

## Prompt

```
You are verifying every citation in the [KB_NAME] knowledge base.

CITATIONS TO VERIFY:
[Paste the complete list of citations from both v2 drafts — cases,
statutes, regulations, enforcement actions, reports, filings]

TASK:
For each citation, verify via web search:

1. Does the cited source EXIST?
2. Is the citation CORRECT?
   - Case: correct parties, correct reporter, correct year, correct court
   - Statute: correct title, section, subsection
   - Regulation: correct CFR title, part, section
   - Report: correct author, title, date, publisher
3. Does the cited source SAY what the KB claims it says?
   - Verify the specific proposition attributed to the source

ZERO TOLERANCE RULE:
If a citation cannot be verified after reasonable search (3+ queries),
it must be REMOVED from the KB. Not flagged — removed.
"Sounds right" is not verification. Confirmed or gone.

EXCLUSION SAFEGUARD:
Before removing any citation, run at least 3 different search queries.
Log the queries. Only exclude if ALL searches return zero results.
(01-shared-axioms.md Pattern 5: Overcautious Exclusion)

VERIFICATION METHOD (per 04-fact-checker.md):
- Primary sources: court databases, official statute text, SEC EDGAR,
  agency press releases, official SDO records
- Source hierarchy: Tier 1 > Tier 2 > Tier 3
- Record the source URL for every verification

EXECUTION CONSTRAINT:
Do NOT delegate or decompose this verification into sub-tasks or
sub-agents. Execute all verifications yourself, sequentially, in this
session. Sub-agent decomposition risks inconsistent verification
standards and makes it harder to track provenance of each verdict.

OUTPUT FORMAT:
| # | Citation | File | Exists? | Correct? | Says what claimed? | Source URL | Verdict |
|---|----------|------|---------|----------|--------------------|-----------|---------|

Summary: [N] confirmed, [N] corrected, [N] removed
Removed citations (with search queries attempted): [list]
Table of Authorities update: [additions/removals]
```
