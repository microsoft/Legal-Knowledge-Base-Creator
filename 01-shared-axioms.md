# Shared Axioms — KB Creation Framework
# Version: 1.0 | Created: 2026-04-09
# Scope: Read by ALL modules. Contains principles, lessons, voice, error patterns.
# Independence: This document has no dependencies on other modules.

---

## SECTION 1: GOVERNING PRINCIPLES

These principles apply to every module in the framework. They are non-negotiable.

### The Rules

1. **Judgment Beats Formalism** — Default to informed judgment over rigid rules.
2. **Don't Solve Tomorrow's Problems Today** — Avoid over-engineering for hypothetical scenarios.
3. **Lead With the Bottom Line** — Start with the practical answer.
4. **Legally Permissible ≠ Operationally Wise** — Account for optics, scalability, durability.
5. **Consistency Over Purity** — Imperfect but consistent > elegant but uneven.
6. **Avoid Bright Lines You Can't Defend Later** — Hard caps invite edge-case pressure.
7. **Optics Matter** — Apply the reasonable outsider test.
8. **Governance Must Scale With the Organization**
9. **Use Structure to Enable Participation, Not Police It**
10. **Explain Tradeoffs—Let Others Decide** — Provide the framework, not just the answer.
11. **Choose—Then Own It** — Apply consistently going forward.

### Quality Axioms

12. **No compromise on quality. Ever.** A KB with a 12% error rate in legal citations is not "acceptable with caveats" — it's wrong law that propagates into legal advice.
13. **Currency is the highest priority for legal KBs.** A repealed statute cited as current law is a critical error. A stale market cap is an annoyance. Different KB types have different error tolerances.
14. **Verify the noun, not just the number.** A correct number attributed to the wrong entity is more dangerous than a fabricated number, because it survives casual verification.
15. **Process is substance.** An auditable process surfaces non-compliance. A silent process hides it.
16. **Every claim must earn its place.** If you can't verify it, say so explicitly or omit it. There is no middle ground.

---

## SECTION 2: VOICE & TONE

### For Legal Knowledge Bases

- **Analytical, not encyclopedic.** Write for a practicing lawyer who needs to act on the information, not a student who needs to learn the background.
- **Lead with the conclusion.** State the legal position, then explain the reasoning.
- **Plain English with precise legal nouns.** Use "governance," "FRAND commitment," "consent decree," "exclusionary conduct." Avoid legalese that obscures meaning.
- **Short paragraphs.** 1-3 sentences. One idea per paragraph.
- **Explain tradeoffs, not rules.** Use concrete examples.
- **Flag risk without alarmism.** Frame uncertainty as risk identification, not speculation.
- **Epistemic humility on facts.** "My understanding is…" / "[UNVERIFIED]" when uncertain about premises. Never hedge on the analytical conclusion.

### For Company Knowledge Bases

- Same analytical tone, focused on strategic implications for CELA practitioners.
- Frame each topic from the perspective of: "What does a Microsoft lawyer need to know about this company's IP/standards/regulatory posture?"
- Include peer comparisons and competitive context where relevant.

### Vocabulary

**Use:** "From a [X] perspective…", "My sense is…", "At a high level…", "That said,…", "The question we need to answer is…", "As a practical matter…", "Realistically…"

**Never use:** "It depends" (without then giving the answer), "pursuant to," "hereinafter," "synergies," "leverage" (as verb), "circle back," "touch base"

---

## SECTION 3: ERROR PATTERN TAXONOMY

These 11 error patterns were identified across 19 company KB builds and 15 topic KB audits. Every module that creates or verifies content should watch for these.

### Pattern 1: Date Drift (4.6% of errors)
**What:** Dates shift by days or months from the actual event.
**Example:** IPO date stated as "December 19, 1991" when actual was December 13, 1991.
**Root cause:** Models reconstruct dates from approximate memory rather than looking them up.
**Detection:** Verify every specific date against primary sources.

### Pattern 2: Attribution Confusion (3.4% of errors)
**What:** Correct number attached to wrong entity.
**Example:** $18.4B was Bloomberg's valuation estimate, not the Amazon deal's valuation.
**Root cause:** Models retrieve numbers and attach them to the most contextually prominent entity.
**Detection:** For every specific number, verify the exact entity, time period, and context.

### Pattern 3: Speculative Inflation (2.3% of errors)
**What:** Plausible-sounding forward numbers stated without sources.
**Example:** "FY2026 revenue projected at $6.40B" when no such projection was public.
**Root cause:** Models extrapolate from trends and present the extrapolation as fact.
**Detection:** Any forward-looking number requires a specific, citable source.

### Pattern 4: Terminology Upgrade (1.1% of errors)
**What:** Informal terms replaced with more precise-sounding but different terms.
**Example:** "spending" → "capex" (which has a specific accounting meaning).
**Root cause:** Models optimize for precision of language, inadvertently changing meaning.
**Detection:** Compare against source material for terminology shifts.

### Pattern 5: Overcautious Exclusion (CRITICAL)
**What:** Real events excluded as fabrications because they seem implausible.
**Example:** Anthropic Pentagon supply-chain risk designation excluded as "fabrication." Adobe $150M FTC settlement removed as "unverified." Both were real.
**Root cause:** Fact-checkers develop a heuristic that unusual claims from known-fabrication models are probably fake. Right ~70% of the time — but creates confirmation bias.
**Detection:** Mandatory web search before ANY exclusion decision. Log the search queries and results.

### Pattern 6: Stale Financial Ratios (CRITICAL)
**What:** Financial ratios use training-data-era numbers when the actual values have moved dramatically.
**Example:** Tesla P/E stated as 40-60× when actual was 294-335×.
**Root cause:** Models cannot know current-period valuations by definition.
**Detection:** All financial ratios, market cap, headcount, and market share require web verification. NEVER trust model knowledge for these fields.

### Pattern 7: Wrong Jurisdiction (1% of errors)
**What:** Legal actions attributed to wrong court, agency, or jurisdiction.
**Root cause:** Models conflate parallel proceedings across jurisdictions.
**Detection:** Verify court name, docket number, and jurisdiction for every case citation.

### Pattern 8: Standards Membership Unverifiability (systemic)
**What:** SDO membership claims that can't be confirmed because many SDOs don't publish rosters.
**Example:** Apple Bluetooth SIG membership — almost certainly true but not publicly confirmable.
**Root cause:** Models assert membership based on product implementation evidence.
**Detection:** Prefer implementation claims over membership claims. For membership, only assert where a public roster, press release, or company filing confirms.

### Pattern 9: Private/Nonprofit Opacity (systemic)
**What:** Revenue, headcount, or operational data for private/nonprofit entities that don't file public reports.
**Root cause:** Models fill gaps with plausible estimates.
**Detection:** Flag all private/nonprofit data claims for heightened scrutiny. Accept "not publicly reported" as an answer.

### Pattern 10: Version Extrapolation
**What:** Commitments for one standard version extrapolated to another.
**Example:** Adobe's RF patent pledge for ISO 32000-1 extrapolated to ISO 32000-2.
**Root cause:** Models treat related standard versions as fungible.
**Detection:** Each version requires independent verification. Wi-Fi 6 ≠ Wi-Fi 7. 4G ≠ 5G.

### Pattern 11: Entity Suffix Fabrication (2% of errors)
**What:** Corporate entity names fabricated or confused (parent vs. subsidiary, LLC vs. Inc.).
**Root cause:** Models guess at corporate structure.
**Detection:** Verify exact entity names from SEC filings or official registrations.

---

## SECTION 4: MODEL BEHAVIOR PROFILES

### Standard Pipeline Configuration

| Role | Model | Family |
|------|-------|--------|
| **Lead** | Claude Opus 4.6 | Anthropic |
| **Builder A** | GPT-5.4 | OpenAI |
| **Builder B** | Goldeneye | Internal |

Three different families ensures maximum divergence in builder drafts and unbiased Lead adjudication.

### Claude Opus 4.6 (Lead)
- **Strengths:** Excellent at comparison, synthesis, and assembly. Best judgment for Lead Reviewer role. Strong at identifying disputes between builders.
- **Weaknesses:** Expensive. Can over-summarize if not explicitly instructed to preserve detail.
- **Role:** Lead Reviewer, final assembly, comparison reports. Never builds content it later judges.
- **Cost:** $5.00/M input, $25.00/M output.

### GPT-5.4 (Builder A)
- **Strengths:** Zero fabrication rate across 19 KBs and 114+ operations. Best sourcing discipline. Current through training data. Excellent for fact-checking, deepening, and post-assembly work.
- **Weaknesses:** Slightly less creative/expansive in first-draft generation.
- **Role:** Builder A (primary builder). Also used for deepening, fact-check, and audit passes.
- **Cost:** $1.75/M input, $14.00/M output. ~$0.70/file for deepening.

### Goldeneye (Builder B)
- **Strengths:** Internal model with distinct training characteristics. Provides genuine third-family divergence.
- **Weaknesses:** Behavior profile still accumulating production data. Monitor for fabrication patterns.
- **Role:** Builder B (independent second draft).

### Claude Sonnet 4.5 (Legacy — No Longer Default)
- **Strengths:** Creative, expansive first drafts. Good at identifying topic areas and structural organization. Richer prose.
- **Weaknesses:** Fabrication rate 6-30%. Most "fabrications" are misattributions, not pure inventions. Often reasserts claims that have been removed by prior passes. Currency issues — frequently uses training-data-era facts.
- **Note:** Replaced by GPT-5.4 as Builder A and Goldeneye as Builder B. Acceptable as a fallback builder if Goldeneye is unavailable.
- **Cost:** $3.00/M input, $15.00/M output.

### Multi-Model Pipeline Rationale
Using all three models in their strongest roles produces better results than any single model. GPT-5.4's accuracy + Goldeneye's divergence + Opus's judgment = the errors each model makes are caught by the others. Single-model pipelines are acceptable only with domain justification and stricter quality floors.

---

## SECTION 5: ERROR-RATE THRESHOLDS

### Post-Audit Disposition (Mandatory)

**Legal / Regulatory Topic KBs:**
| Error Rate | Action |
|-----------|--------|
| 0-4%      | Ship — corrections only |
| 8%        | Targeted corrections + re-audit the corrected claims |
| 12%+      | **Full multi-model rebuild** |

**Company / Entity KBs:**
| Error Rate | Action |
|-----------|--------|
| 0-8%      | Ship — corrections only |
| 12-16%    | Targeted corrections + re-audit |
| 20%+      | **Full multi-model rebuild** |

**Rationale:** Legal accuracy has a categorically different error tolerance than financial data staleness. A wrong statute citation propagates into legal advice. A stale market cap does not.

These thresholds apply to ALL audits: initial, post-ship, refresh, and periodic re-verification.

---

## SECTION 6: SIZE GATES

### Pre-Ship Size Gate (Mandatory)
- **Hard floor:** No topic file below 12KB. Blocking deficiency.
- **Soft floor:** Average topic file ≥ 15KB. Triggers mandatory deepening if below.
- **Parity check:** Thinnest file ≥ 50% of thickest file.

### Rebuild Target Sizes
- **Company KB topic files:** 18-30KB each
- **Legal topic KB files:** 15-30KB each
- **Litigation analysis files:** 30-50KB each (deeper procedural detail required)
- **Index files:** 10-20KB
- **Table of authorities:** 10-20KB
- **AGENTS.md:** 5-7KB

---

## SECTION 7: LESSONS LEARNED

Numbered lessons from production. New lessons are added via the Option C promotion process: when the Fact-Checker identifies the same error pattern in 3+ KBs, it drafts a lesson here. Human confirms or edits.

1. Models fabricate with confident specificity — invented case names, fake docket numbers, plausible-sounding enforcement actions.
2. Date and citation errors are more common than invented cases.
3. Financial data is always stale from model knowledge alone.
4. Two-model comparison catches errors that single-model review misses.
5. Three model families ideal; two acceptable with domain justification.
6. Builder drafts should be preserved in `sources/` — they are raw material for future deepening.
7. Deepening is a pipeline stage, not remediation. Every build requires it.
8. Assembly compression is structural: two 70-100KB drafts compress to 6-10KB/file average. This is not failure — it's the mathematics of merging overlapping content.
9. Max 2-3 concurrent agents for high-context phases. Deepening can do 4-6. Never exceed 20.
10. Per-file quality floors matter more than averages. Four weak files in a 16-file KB will be found.
11. Overstated legal conclusions propagate through downstream work products.
12. Implausibility is not evidence of fabrication — it's a signal to search harder.
13. Mandatory web search before any exclusion decision.
14. High-volatility fields (P/E, market share, headcount, valuations, patent counts) are NEVER trustworthy from model knowledge.
15. Verify the noun, not just the number.
16. Standards membership ≠ standards implementation — and membership is often unverifiable.
17. Never extrapolate patent or FRAND commitments across standard versions.
18. Pipeline fatigue produces thinner later builds — size gate catches this.
19. Most "fabrications" are misattributions — correct facts with wrong context.
20. Legal topic KBs require stricter error-rate thresholds than company KBs.
21. Deepening without the full pipeline introduces errors. Deepening should feed INTO the pipeline, not bypass it.
22. Prior audits can introduce new errors — verification passes need their own verification.
23. Sonnet builds creative but stale; GPT-5.4 builds precise and current. Use each in its strength.

*Lessons are added when the same pattern is observed in 3+ KBs. The master audit log tracks pattern frequency.*

---

## SECTION 8: HANDOFF ARTIFACT FORMATS

Each module communicates with others through artifacts, not by reading each other's instructions. These are the standard formats.

### Info Gather → Builder
```
File: sources/scoping_report.md
Contains:
- KB name, type (company/legal topic/litigation/person)
- Audience and purpose
- Topic list with brief descriptions
- Coverage boundaries (what's in/out)
- Known verified anchor facts
- Key sources to consult
- Target size and file count
```

### Builder → Fact-Checker
```
File: The assembled KB files themselves
Plus: sources/build_manifest.md listing all files, sizes, and models used
```

### Fact-Checker → Builder/Shared Axioms
```
File: sources/factcheck_report.md
Contains:
- Numbered claim list with verdicts (Confirmed/Wrong/Stale/Unverifiable)
- Source citations for each verification
- Error rate calculation
- Error pattern tags (from taxonomy in Section 3)
- Suggested corrections
- Pattern frequency counts (for Option C promotion)
```

### Fact-Checker → Master Audit Log
```
Appends to: KB Creation Framework/master_audit_log.md
Format: Date | KB Name | Error Rate | Patterns Found | Disposition
```

### Orchestrator → All Modules
```
File: sources/pipeline_state.md
Contains:
- Current phase, what's complete, what's pending
- Decision log (why rebuild vs. patch, which builder is backbone)
- Agent IDs and status
- Handoff checklist
```

---

*This document is the foundation of the KB Creation Framework. All other modules reference it. It is updated only through the Option C promotion process or direct human edit.*
