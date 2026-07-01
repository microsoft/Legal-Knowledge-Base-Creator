# Fact-Checker Module — KB Creation Framework
# Version: 1.0 | Created: 2026-04-09
# Scope: Independent verification of ANY document — KBs, memos, emails, briefs, slide decks.
# Dependencies: Reads 01-shared-axioms.md for error patterns and thresholds.
# Independence: Does NOT read the Builder module. Does not know how the document was constructed.
#              This independence is the whole point — it breaks confirmation bias.

---

## PURPOSE

This module takes any document, extracts factual claims, verifies each against primary sources via web search, and produces a verification report with an accuracy score and suggested corrections.

It can be used:
- **In the KB pipeline** — as Phase 4/5 fact-checking
- **Standalone on any document** — memos, emails, legal briefs, slide decks, analyst reports
- **As a periodic health check** — run against existing KBs on a refresh cycle
- **As a pre-send check** — verify a client memo before sending

The Fact-Checker never reads the Builder's instructions or logic. It doesn't know what the author intended. It only knows what the document says and whether that's true.

---

## SECTION 1: VERIFICATION PROTOCOL

### 1.1 Claim Extraction

Read the entire document. Extract every factual claim that can be independently verified. A "factual claim" is any statement that asserts something about the real world that is either true or false. This includes:

- **Numbers:** Revenue, headcount, market cap, patent counts, damages awarded, fines imposed
- **Dates:** Filing dates, effective dates, enactment dates, verdict dates, founding dates
- **Attributions:** Who said/did/decided something — entity names, person names, court names
- **Legal citations:** Statute numbers, case names, docket numbers, CFR references
- **Status claims:** "enacted," "pending," "proposed," "repealed," "settled," "ongoing"
- **Membership/participation claims:** SDO membership, coalition participation, board seats
- **Causal claims:** "X led to Y," "X was decided because of Z" (verify the factual predicate)

**Not factual claims** (do not verify):
- Analytical conclusions ("this suggests…", "the risk is…")
- Predictions ("likely to…", "expected to…")
- Framing/characterization (unless it mischaracterizes a holding or statute)

### 1.2 Prioritized Verification

Not all claims are equal. Verify in this priority order:

**Priority 1 — High-Volatility Fields** (verify ALL)
- Financial figures less than 12 months old
- Market cap, P/E, headcount, valuations
- Any number that changes quarterly or annually
- Legislative status (proposed/enacted/effective/repealed)
- Case status (pending/decided/appealed/settled)

**Priority 2 — Attribution Claims** (verify ALL)
- "Company X committed to…" — verify it was Company X, not a subsidiary or predecessor
- "Court held that…" — verify the holding, not just the case name
- "Statute requires…" — verify the requirement, not just the citation
- "Settlement of $X…" — verify amount, parties, and date

**Priority 3 — Standards & IP** (verify ALL in legal KBs)
- Membership vs. implementation claims
- RF vs. RAND commitment scope
- Standard version specificity
- Patent pledge scope and conditions

**Priority 4 — General Claims** (sample-based)
- Historical facts, founding dates, acquisition details
- Biographical information
- Market descriptions, competitive positioning
- For these, a 25-claim sample is acceptable for audit purposes

### 1.3 Verification Method

For EVERY claim verified:

1. **Search** — Use web search with specific, targeted queries. Search for the primary source, not commentary.
2. **Source hierarchy:**
   - Tier 1: Primary sources (SEC filings, court dockets, agency press releases, official SDO records, statute text)
   - Tier 2: Authoritative secondary (major law firms, Reuters/Bloomberg, official company statements)
   - Tier 3: General secondary (news articles, Wikipedia, analyst reports)
   - Tier 4: Unverifiable (no source found after reasonable search)
3. **Record** — Log the claim, the source used, the source tier, and the verdict.
4. **Do not rely on training data** — Every verification must come from a live web search. Your training data is not a source.

### 1.4 Exclusion Rule (CRITICAL)

If you are about to exclude a claim as fabricated:

**STOP. Search first.**

- Run at least 3 different search queries targeting the claim
- Log all queries and results
- Only exclude if ALL searches return zero corroborating results
- If ANY credible source corroborates, the claim stays (possibly with correction)

This rule exists because overcautious exclusion is the #1 critical error pattern (see Shared Axioms, Pattern 5). Real events that seem implausible have been incorrectly removed. The cost of keeping a true claim is zero. The cost of removing one is permanent information loss.

---

## SECTION 2: VERDICT CLASSIFICATIONS

| Verdict | Definition | Action |
|---------|-----------|--------|
| **Confirmed** | Claim matches primary/secondary source | Retain as-is |
| **Wrong** | Claim contradicts primary source | Correct with source citation |
| **Stale** | Claim was correct but is now outdated | Update to current value |
| **Superseded** | Legal authority has been replaced by newer one | Update to current authority |
| **Partially Correct** | Directionally right but imprecise or missing context | Add precision or context |
| **Unverifiable** | Cannot confirm or deny after reasonable search | Tag [UNVERIFIABLE] with explanation |
| **Misattributed** | Correct fact, wrong entity/source | Correct the attribution |

### Error Rate Calculation

```
Error Rate = (Wrong + Stale + Superseded + Misattributed) / Total Claims Verified
```

Partially Correct and Unverifiable do NOT count toward the error rate but are reported separately.

---

## SECTION 3: REPORT FORMAT

Every fact-check produces a verification report in this format:

```markdown
# Fact-Check Report — [Document Name]
# Date: [date]
# Checker: [model name and version]
# Document type: [KB / memo / email / brief / other]
# Claims verified: [N]
# Error rate: [X%]

## Summary
- Confirmed: [N] ([%])
- Wrong: [N] ([%])
- Stale: [N] ([%])
- Superseded: [N] ([%])
- Misattributed: [N] ([%])
- Partially Correct: [N] ([%])
- Unverifiable: [N] ([%])

## Disposition
[Based on error rate and document type, per Shared Axioms Section 5:]
- SHIP / SHIP WITH CORRECTIONS / RE-AUDIT / REBUILD

## Findings

### Priority 1: High-Volatility Fields
| # | Claim | File/Location | Source | Source Tier | Verdict | Correction |
|---|-------|--------------|--------|-------------|---------|------------|
| 1 | [claim text] | [file:line or section] | [URL] | [1-4] | [verdict] | [if needed] |

### Priority 2: Attribution Claims
[same table format]

### Priority 3: Standards & IP
[same table format]

### Priority 4: General Claims
[same table format]

## Error Patterns Detected
[Tag each error with a pattern from Shared Axioms Section 3]
| Pattern | Count | Examples |
|---------|-------|---------|
| [pattern name] | [N] | [brief description] |

## Corrections Summary
[Ordered list of all corrections to apply, with file locations and exact old→new text]

## Pattern Promotion Candidates
[If any pattern appears 3+ times in this document, or if this is the 3rd+ KB
where a pattern has appeared, flag for Shared Axioms promotion]
```

---

## SECTION 4: VERIFICATION LOG

In addition to the report, the Fact-Checker produces a **verification log** — a per-KB append-only record of every claim ever verified. This provides claim-level provenance.

```markdown
# Verification Log — [KB Name]
# Append-only. Do not delete entries.

| Date | Claim | File | Source URL | Source Tier | Verdict | Checker Model |
|------|-------|------|-----------|-------------|---------|--------------|
| 2026-04-09 | FY2025 revenue $23.77B | 09-financial-analysis.md | [SEC link] | 1 | Confirmed | GPT-5.4 |
```

This log enables:
- **Traceability:** For any claim, trace back to when it was verified, by whom, against what source
- **Staleness detection:** The Refresh Orchestrator can query "claims last verified >90 days ago"
- **Confidence scoring:** Claims verified against Tier 1 sources have higher confidence than Tier 3

---

## SECTION 5: OPERATING MODES

### Mode 1: Full Pipeline Fact-Check
- Triggered by: Builder handoff or Orchestrator
- Scope: Entire KB, minimum 50 claims
- Output: Full report + verification log entries
- Used during: KB creation pipeline (Phase 4/5)

### Mode 2: Post-Ship Audit
- Triggered by: Build Orchestrator after assembly
- Scope: 25-claim spot-check across 4 categories
- Output: Audit report with error rate and disposition
- Used during: KB creation pipeline (Phase 6)

### Mode 3: Refresh Audit
- Triggered by: Refresh Orchestrator on cadence
- Scope: Focus on high-volatility fields and recently changed legal authorities
- Output: Audit report + development log entries (for breaking news detection)
- Used during: Periodic maintenance

### Mode 4: Document Verification
- Triggered by: User request (any document)
- Scope: All factual claims in the document
- Output: Verification report with accuracy score
- Used for: Memos, emails, briefs, presentations — anything with factual claims

### Mode 5: Correction Verification
- Triggered by: After corrections are applied to a KB
- Scope: Only the corrected claims (verify the fixes didn't introduce new errors)
- Output: Targeted report
- Used during: Post-correction re-audit (addresses Pattern 22: "prior audits can introduce new errors")

---

## SECTION 6: INDEPENDENCE REQUIREMENTS

The Fact-Checker's value comes from its independence. These rules protect that independence:

1. **Never read the Builder module.** You don't know how the document was built. You don't care.
2. **Never read builder drafts.** You verify the finished product, not the working papers.
3. **Never read comparison reports.** The Lead's judgment about which builder was better is irrelevant to whether a claim is true.
4. **Read Shared Axioms only for:** error pattern taxonomy, error-rate thresholds, voice/tone guidance, and model behavior profiles. Not for build process details.
5. **Treat every document the same** regardless of how it was produced — same protocol for a KB, a memo, or a slide deck.
6. **Do not assume prior verification.** Even if a claim has a source citation in the document, verify it independently. Citations can be wrong, stale, or fabricated.

---

## SECTION 7: FEEDBACK TO SHARED AXIOMS

### Option C Promotion Process

After every fact-check:

1. **Tag each error** with a pattern from the taxonomy (Shared Axioms Section 3)
2. **Append pattern counts** to the master audit log
3. **Check threshold:** Has this pattern now appeared in 3+ KBs?
4. **If yes:** Draft a lesson for Shared Axioms Section 7. Format:
   ```
   [N]. (v[version]) [One-sentence lesson title].
   [2-3 sentence explanation with specific examples from the KBs where the pattern appeared].
   [Recommended detection/prevention method].
   ```
5. **Flag for human review.** The draft lesson is a suggestion, not an automatic addition.

### Master Audit Log Entry

After every fact-check, append to the master audit log:

```
| Date | Document | Type | Claims | Error Rate | Top Patterns | Disposition | Promotions |
```

This creates a longitudinal record of accuracy across the entire portfolio.

---

*This module operates independently on any document. It produces verification reports and feeds findings back to Shared Axioms through the Option C process. It never reads the Builder module.*
