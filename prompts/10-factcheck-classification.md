# Fact-Check: Classification Verification (Decomposed — Phase 5c)

**Role:** Lead or dedicated fact-check agent
**Input:** Classification claims from v2 drafts
**Output:** Classification verification results
**Runs:** Once per build; can run in parallel with 5a and 5b
**Why decomposed:** Highest-impact legal check; deserves full attention
**Reference:** 04-fact-checker.md for full verification protocol

---

## Prompt

```
You are verifying the classifications and categorizations in the
[KB_NAME] knowledge base.

CLASSIFICATIONS TO VERIFY:
[Paste the list of classification claims — statutory tiers, regulatory
categories, entity types, standard designations, commitment types]

TASK:
For each classification claim, verify via web search:

1. STATUTORY TIER MAPPINGS:
   - Is the activity classified under the correct statute section?
   - Is the regulatory tier (e.g., Tier I vs. Tier II) correct?
   - Does the classification match the actual statutory text?

2. ENTITY CLASSIFICATIONS:
   - Corporate structure: parent vs. subsidiary, LLC vs. Inc.
   - Regulatory status: registered vs. exempt, domestic vs. foreign
   - Verify from SEC filings or official registrations

3. STANDARD DESIGNATIONS:
   - RF vs. RAND commitment scope — verify the exact commitment
   - Standard version specificity — don't extrapolate across versions
   - Membership vs. implementation claims
   (01-shared-axioms.md Pattern 10: Version Extrapolation)

4. COMMITMENT TYPE CLAIMS:
   - "Voluntary" vs. "mandatory" — verify the legal basis
   - "Binding" vs. "non-binding" — verify enforceability
   - Consent decree terms — verify exact obligations

VERIFICATION METHOD:
- Primary sources: statute text, regulatory filings, SDO records,
  consent decree text, official patent declarations
- NEVER infer a classification — verify it from source text
- Entity suffix accuracy: verify exact legal name
  (01-shared-axioms.md Pattern 11: Entity Suffix Fabrication)

EXECUTION CONSTRAINT:
Do NOT delegate or decompose this verification into sub-tasks or
sub-agents. Execute all verifications yourself, sequentially, in this
session. Sub-agent decomposition risks inconsistent verification
standards and makes it harder to track provenance of each verdict.

OUTPUT FORMAT:
| # | Claim | Category | As Classified | Correct Classification | Source | Verdict |
|---|-------|----------|--------------|----------------------|--------|---------|

Summary: [N] confirmed, [N] corrected, [N] unverifiable
BLOCKING items (wrong statutory tier or commitment type): [list]
Corrections to apply: [old → new with source citation]
```
