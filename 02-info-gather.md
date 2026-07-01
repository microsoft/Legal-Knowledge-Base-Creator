# Info Gather Module — KB Creation Framework
# Version: 1.0 | Created: 2026-04-09
# Scope: Standalone scoping and requirements elicitation for any KB project.
# Dependencies: Reads 01-shared-axioms.md for voice and principles.
# Independence: Can operate alone to scope a project without triggering a build.

---

## PURPOSE

This module produces a **scoping report** that defines what a knowledge base should cover, who it serves, and what quality standards apply. It can be used:

- **Standalone** — to scope a project for future execution
- **As Phase 0** — feeding into the Builder module via artifact handoff

The output is a `scoping_report.md` file that any Builder (human or AI) can execute against.

---

## SECTION 1: REQUIRED QUESTIONS

Before building any KB, these questions must be answered. Do not proceed to building until every required question has a clear answer.

### 1.1 Identity & Type

| Question | Why It Matters |
|----------|---------------|
| **What is this KB about?** (company, legal topic, litigation, person, technology) | Determines structure, voice, error thresholds, and size targets |
| **What type of KB is this?** | Company KB, Legal Topic KB, Litigation Analysis KB, Person KB, Technology KB |
| **Does a KB on this topic already exist?** | If yes: audit first, decide rebuild vs. patch. Don't duplicate. |

### 1.2 Audience & Purpose

| Question | Why It Matters |
|----------|---------------|
| **Who will use this KB?** (role, seniority, organization) | Drives depth, tone, and assumed knowledge level |
| **What decisions will this KB inform?** | Determines which topics are essential vs. nice-to-have |
| **What's the primary use case?** (reference, analysis, briefing prep, client advice) | Shapes structure — reference KBs need routing tables; analysis KBs need conclusions |

### 1.3 Scope & Boundaries

| Question | Why It Matters |
|----------|---------------|
| **What topics MUST be covered?** | Non-negotiable coverage areas |
| **What topics are explicitly OUT of scope?** | Prevents scope creep — "oxygen expands to the size of the room" |
| **What time period?** (historical through current, current only, specific date range) | Determines depth of historical coverage |
| **What jurisdictions?** (US only, EU, global, specific countries) | Legal KBs especially — jurisdiction determines which statutes matter |
| **What level of detail?** (survey, practitioner-depth, expert-depth) | Drives file size targets |

### 1.4 Known Anchors

| Question | Why It Matters |
|----------|---------------|
| **What facts are already verified?** | Anchor points that builders must preserve — prevents regression |
| **What are the key sources?** (SEC filings, court dockets, SDO websites, specific reports) | Directs builders to primary sources |
| **Are there existing materials to incorporate?** (prior KBs, memos, research) | Avoids duplicate work |
| **What are known controversies or disputed facts?** | Flags areas requiring extra verification |

### 1.5 Quality Requirements

| Question | Default | Override? |
|----------|---------|-----------|
| **Error-rate threshold?** | 12% legal, 20% company | Only stricter |
| **Size targets?** | Per Shared Axioms Section 6 | Can increase, not decrease |
| **Multi-model pipeline?** | Required for legal KBs | Single-model only with justification |
| **Post-ship audit?** | Mandatory | Never waived |

---

## SECTION 2: TOPIC STRUCTURE DEVELOPMENT

After answering the required questions, develop the topic structure.

### 2.1 For Company KBs

Standard 14-topic structure (customize as needed):

1. Company Overview & Business Model
2. Patent Portfolio & Technology
3. IP Monetization & Licensing
4. Standards Participation
5. FRAND/SEP Positions
6. Litigation History
7. ITC Proceedings
8. Antitrust & Regulatory
9. Financial Analysis
10. AI Technology & IP Strategy
11. Competitive Positioning
12. Corporate History & Key Figures
13. Public Policy Positions
14. [Company]-Microsoft Relationship

**Customization rules:**
- Add topics for company-specific areas (e.g., Adobe → Content Authenticity)
- Remove topics that don't apply (e.g., ITC if company has no ITC history)
- Never remove: Overview, IP/Patents, Litigation, Financial, Competitive

### 2.2 For Legal Topic KBs

No standard structure — topics emerge from the subject matter. But every legal topic KB should include:

- **Operating map / regulatory landscape** (which agencies, which statutes, which jurisdictions)
- **Key statutes and regulations** (full text analysis, not just citations)
- **Leading cases** (holdings, not just names)
- **Compliance frameworks** (what practitioners need to do)
- **Emerging issues** (what's changing, what's pending)
- **Cross-references** to related topic KBs

### 2.3 For Litigation Analysis KBs

Structure follows the cases:
- **Per-case files** (procedural history, legal theories, holdings, implications)
- **Cross-case analysis** (common themes, doctrinal shifts, strategic implications)
- **Implications file** (what this means for the organization)

### 2.4 Topic Sizing

| KB Type | Files | KB/file target | Total target |
|---------|-------|---------------|-------------|
| Company (14 topics) | 14-16 | 18-30KB | 250-400KB |
| Legal Topic (10-18 topics) | 12-20 | 15-30KB | 200-500KB |
| Litigation Analysis (2-5 cases) | 4-8 | 30-50KB | 150-300KB |
| Person | 5-8 | 15-25KB | 75-200KB |

---

## SECTION 3: SCOPING REPORT FORMAT

The output of this module is a single markdown file that serves as the handoff artifact to the Builder.

```markdown
# Scoping Report — [KB Name]
# Date: [date]
# Scoped by: [human/model]

## KB Identity
- **Name:** [name]
- **Type:** [company / legal topic / litigation / person]
- **Location:** [file path]

## Audience & Purpose
- **Primary audience:** [role, seniority, organization]
- **Primary use case:** [reference / analysis / briefing prep]
- **Decisions informed:** [list]

## Scope
- **Topics in scope:** [numbered list with brief descriptions]
- **Topics out of scope:** [explicit exclusions]
- **Time period:** [range]
- **Jurisdictions:** [list]
- **Detail level:** [survey / practitioner / expert]

## Known Anchors
- **Verified facts:** [list with sources]
- **Key sources:** [list]
- **Existing materials:** [file paths]
- **Known controversies:** [list]

## Quality Requirements
- **Error-rate threshold:** [X%]
- **Size targets:** [per-file and total]
- **Pipeline:** [multi-model / single-model with justification]
- **Post-ship audit:** [mandatory]

## Topic Structure
[Numbered list of proposed topics with 1-2 sentence descriptions]

## Builder Notes
[Any additional context, warnings, or priorities for the builders]
```

---

## SECTION 4: ANTI-PATTERNS

### Don't scope blind
Read existing materials before scoping. The user caught us about to rebuild three KBs without understanding their scope, audience, or purpose. Always read index.md, AGENTS.md, and sample content files before defining the rebuild plan.

### Don't assume standard structure
Not every KB fits the 14-topic company template. Litigation KBs have 3-5 deep case files. Topic KBs have domain-specific structures. Let the subject matter drive the structure.

### Don't scope too broadly
"Cover all gaming law" is 18 topics. "Cover the two Meta verdicts" is 3 files. Scope determines resource allocation. Over-scoping produces thin coverage across too many topics. Under-scoping produces deep coverage but misses important areas. Match scope to purpose.

### Don't skip the "out of scope" question
If you don't explicitly exclude topics, they'll creep in. "Oxygen expands to the size of the room." The Builder will find adjacent topics interesting and start covering them unless told not to.

---

*This module operates independently. Its only output is a scoping_report.md file. It does not build, verify, or assemble anything.*
