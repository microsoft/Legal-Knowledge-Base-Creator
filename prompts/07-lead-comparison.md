# Lead Comparison Prompt (Monolithic — Phase 4)

**Role:** Lead reviewer
**Input:** Both builders' v2 drafts
**Output:** Comparison report with dispute resolutions
**Runs:** Once
**Why monolithic:** Lead needs both v2 drafts simultaneously for comparison

---

## Prompt

```
You are the Lead reviewer comparing two independently-built drafts
of the [KB_NAME] knowledge base.

BUILDER A ([model name]) v2 DRAFT:
[Paste or attach Builder A's v2 draft]

BUILDER B ([model name]) v2 DRAFT:
[Paste or attach Builder B's v2 draft]

SCOPING REPORT TOPICS: [TOPICS_IN_SCOPE]

TASK:
Compare both drafts section by section. Produce a comparison report
that will drive assembly.

FOR EACH SECTION, evaluate:

1. AGREEMENTS — Claims both builders make. These are likely correct.
   Note them with confidence level.

2. DISPUTES — Claims where builders disagree. For each:
   - State what Builder A says
   - State what Builder B says
   - Web search to resolve the dispute
   - Record your resolution with source citation
   Do NOT just flag disputes — SETTLE them.

3. UNIQUE CONTENT — Material in one builder but not the other.
   - Evaluate: is it accurate? (web search)
   - Evaluate: is it in scope?
   - Recommend: include, exclude, or verify further

4. FABRICATION CHECK — Claims appearing in only one builder that
   seem implausible. Apply the exclusion rule:
   SEARCH FIRST. Run at least 3 queries before excluding.
   (See 01-shared-axioms.md Pattern 5: Overcautious Exclusion)

5. BACKBONE RECOMMENDATION — For each section, which builder's
   version should be the backbone for assembly? Consider:
   - Factual accuracy
   - Depth and specificity
   - Citation quality
   - Analytical value

OUTPUT FORMAT:
sources/comparison_report.md

Section-by-section table:
| Section | Backbone | Agreements | Disputes (resolved) | Unique A | Unique B |
Then detailed findings for each section.
Overall backbone recommendation: [Builder A / Builder B / mixed]
```
