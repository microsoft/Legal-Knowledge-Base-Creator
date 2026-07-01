# Fact-Check: Currency Verification (Decomposed — Phase 5a)

**Role:** Lead or dedicated fact-check agent
**Input:** Claim list from v2 drafts (statutes, cases, regulations)
**Output:** Currency verification results
**Runs:** Once per build; can run in parallel with 5b and 5c
**Why decomposed:** Discrete verification task; full attention prevents "run out of steam"
**Reference:** 04-fact-checker.md for full verification protocol

---

## Prompt

```
You are verifying the currency of legal authorities cited in the
[KB_NAME] knowledge base.

CLAIMS TO VERIFY:
[Paste the list of statutes, cases, and regulations from both v2 drafts]

TASK:
For each legal authority cited, verify via web search:

1. STATUTES:
   - Is the statute still in force?
   - Has it been amended since the date cited?
   - Has it been repealed or superseded?
   - Is the section number cited correct?

2. CASES:
   - Is the case still good law?
   - Has it been reversed, vacated, or distinguished?
   - Is the holding as characterized in the KB?

3. REGULATIONS:
   - Is the regulation current?
   - Has the CFR section been updated?
   - Are any proposed rules pending that would change it?

VERIFICATION METHOD (per 04-fact-checker.md):
- Web search with specific, targeted queries
- Use primary sources: official statute text, court databases, CFR
- Source hierarchy: Tier 1 (primary) > Tier 2 (authoritative) > Tier 3 (general)
- Do NOT rely on training data

EXECUTION CONSTRAINT:
Do NOT delegate or decompose this verification into sub-tasks or
sub-agents. Execute all verifications yourself, sequentially, in this
session. Sub-agent decomposition risks inconsistent verification
standards and makes it harder to track provenance of each verdict.

VERDICT CLASSIFICATIONS:
- Confirmed: still current and accurately described
- Stale: was correct, now outdated
- Superseded: replaced by newer authority
- Wrong: never correct, or materially mischaracterized

OUTPUT FORMAT:
| # | Authority | Type | As Cited | Current Status | Source | Verdict |
|---|-----------|------|----------|---------------|--------|---------|

Summary: [N] confirmed, [N] stale, [N] superseded, [N] wrong
BLOCKING items (repealed statutes cited as current): [list]
```
