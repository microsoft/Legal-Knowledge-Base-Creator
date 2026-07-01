# Lead Assembly Prompt (Monolithic — Phase 6)

**Role:** Lead assembler
**Input:** Both v2 drafts, comparison report, all fact-check results
**Output:** Complete assembled KB
**Runs:** Once
**Why monolithic:** Cross-cutting synthesis requires full context
**Reference:** 03-builder.md Sections 5-6 for assembly rules and file standards

---

## Prompt

```
You are assembling the final [KB_NAME] knowledge base from two
independently-built and reviewed drafts.

BUILDER A v2 DRAFT: [paste or attach]
BUILDER B v2 DRAFT: [paste or attach]
COMPARISON REPORT: [paste or attach]
FACT-CHECK RESULTS:
- Currency (5a): [paste or attach]
- Citations (5b): [paste or attach]
- Classifications (5c): [paste or attach]

KB TYPE: [KB_TYPE]
AUDIENCE: [AUDIENCE]
SIZE TARGET: [SIZE_TARGET] per file

ASSEMBLY RULES (per 03-builder.md Section 5):

1. Use the BACKBONE builder per comparison report recommendation.
2. Enrich with verified content from the other builder.
3. Apply ALL fact-check corrections — remove unverified citations,
   update stale data, fix misclassifications.
4. Preserve ALL specifics: dates, amounts, citations, entity names.
5. Do NOT summarize. If a builder wrote 20KB on a topic, the
   assembled version must be AT LEAST 20KB.
6. No [UNVERIFIED] tags in final output — resolve or remove.

7. PROVENANCE TRACKING: For any citation, enforcement case, dollar
   amount, or factual claim you add that does NOT appear in either
   builder draft, mark it with <!-- [NEW] --> inline. These will be
   verified in Phase 6b. Do NOT suppress new content — enrichment is
   expected — but make the new claims identifiable.

CREATE THESE FILES:

1. AGENTS.md (create FIRST — 5-7KB)
   - Entry point with routing table
   - Topic-to-file mapping
   - Session protocol

2. index.md (10-20KB)
   - KB snapshot and key dates
   - Quick reference covering the most-asked questions
   - Routing table to deep-dive files

3. Per-topic files: [NN]-[topic-slug].md
   Each file must include:
   - YAML metadata header (title, kb, last_updated, tags)
   - Key Takeaways section (3-5 bullets)
   - Full analytical content
   - Cross-references to related files
   - Inline source citations

4. table-of-authorities.md (10-20KB)
   - Every verified citation organized by type
   - Only citations that passed fact-check Phase 5b

FILE STANDARDS:
- Every file must have YAML front matter
- Analytical voice for [AUDIENCE]
- Lead with conclusions, then reasoning
- Size floor: no file below 12KB (01-shared-axioms.md Section 6)
- Parity: thinnest file ≥ 50% of thickest

After assembly, run the size gate. List every file with its size.
Flag any below 12KB for deepening.

After the size gate, produce a NEW CLAIMS MANIFEST:
List every citation, case, dollar amount, or factual claim in the
assembled output that does not appear in either builder draft.
Format: | File | Claim | Type | Source (if known) |
Save as sources/new_claims_manifest.md.
This manifest feeds Phase 6b (post-assembly fact-check).

OUTPUT: Complete KB directory structure with all files.
Save builder drafts and comparison report in sources/ for provenance.
```
