# Filename Validation (Phase 6a)

**Role:** Lead or automated check
**Input:** Assembled KB directory
**Output:** Validation report — all cross-references verified or flagged
**Runs:** Once, immediately after assembly (Phase 6)
**Why this exists:** Assembly agents consistently use intuitive shortnames for cross-references instead of actual filenames. In the Trade/Export Controls test build, this produced 24 broken links across 10 files — a mechanical error that should never reach the final product.
**Reference:** Learned from pipeline test (2026-04-09)

---

## Prompt

```
You are validating all internal cross-references in the assembled
[KB_NAME] knowledge base.

KB DIRECTORY: [STATE_ROOT]

TASK:

1. LIST all files in the KB directory (topic files, index.md, AGENT.md,
   table-of-authorities.md). Record the exact filename of each.

2. SCAN every file for internal cross-references:
   - Markdown links: [text](filename.md)
   - YAML front matter cross_references fields
   - Inline references to other topic files by filename
   - Routing table entries in AGENT.md and index.md

3. For each cross-reference found, verify the target filename exists
   EXACTLY as written. Case-sensitive. Extension-sensitive.

4. For broken references, determine the correct target:
   - Match by topic number (e.g., "07-encryption-controls.md" → the
     file that starts with "07-")
   - Verify the correct filename from the directory listing
   - Replace the broken reference with the exact correct filename

5. FIX all broken references in-place.

OUTPUT:
| # | Source File | Line | Broken Reference | Corrected To |
|---|-------------|------|-----------------|--------------|

Summary: [N] cross-references checked, [N] correct, [N] fixed.

If all cross-references are correct: report PASS.
If any remain unresolvable: report BLOCKING with details.
```
