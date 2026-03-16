---
name: resume-tailor
description: |
  Tailors a resume to a specific job description with ATS optimization.
  Use when asked to optimize a resume, tailor CV for a job, create an ATS-friendly resume,
  generate a cover letter, or adapt career profile for a specific role.
  Triggers on: "resume", "CV", "tailor", "ATS", "job application", "cover letter",
  "optimize resume", "adapt resume", "резюме", "вакансия".
user-invocable: true
---

# Resume Tailor

Multi-stage skill that transforms a resume into an ATS-optimized document tailored to a specific job description. Supports EN/US and RU/CIS locales. Replaces generic AI resume services with job-specific, strategic tailoring.

## Reference Files

Read these files at the appropriate stage — they contain rules, conventions, and templates:
- `references/ats-rules.md` — ATS formatting rules, keyword strategy, scoring formula, anti-patterns
- `references/locale-en.md` — English/US resume conventions
- `references/locale-ru.md` — Russian/CIS resume conventions
- `references/section-templates.md` — Section templates with slot markers for both locales

## Master Profile

Location: `docs/resume/master-profile.md`

The master profile is a comprehensive data store of the user's career — every role, metric, skill, and transition context. It is NOT a formatted resume. The skill tailors FROM the master profile for each job application.

- If master profile exists: use it as the primary source
- If it doesn't exist: build it from the provided resume on first run
- After each tailoring session: offer to update it with any new information

Master profile structure:
```
# Master Profile
## Contact (name, email, phone, LinkedIn, location)
## Career Narrative (raw, unformatted — the full story)
## Roles (each with: company context, title, dates, team size/structure, responsibilities, achievements with metrics, technologies, transition reason)
## Education
## Certifications
## Skills Inventory (comprehensive, not pre-filtered)
## Languages
## Notes (gaps, side projects, consulting arrangements, etc.)
```

---

## Workflow

```
STAGE 0: Resolve Inputs
    |
STAGE 1: Intake & Analysis (subagent)
    |
STAGE 1.5: Resume Feedback (presented to user)
    |
STAGE 2: Strategic Questions (AskUserQuestion)
    |
STAGE 3: ATS Optimization & Tailoring
    |
STAGE 4: Draft Generation (.md)
    |
STAGE 5: Review & Iteration
    |
STAGE 6: Final Output (.docx + optional cover letter)
```

---

## STAGE 0: Resolve Inputs

### Resume Input
```
IF arguments contain a file path (PDF/MD/TXT):
  Read the file
ELIF docs/resume/master-profile.md exists:
  Read it. Confirm with user: "Using your master profile as the base. Correct?"
ELSE:
  Ask: "Please provide your resume — paste text, give a file path, or attach a PDF."
```

### Job Description Input
```
IF arguments contain a URL (https://...):
  WebFetch the URL
  Extract: title, company, requirements, responsibilities, qualifications
  If fetch returns insufficient content: ask user to paste the JD text instead
ELIF arguments contain pasted text:
  Parse directly
ELSE:
  Ask: "Provide the target job description — paste the text or give me a URL."
```

### First-Run: Master Profile Creation
If no master profile exists and a resume was provided, build the master profile:
1. Parse the resume into the master profile structure (see above)
2. Save to `docs/resume/master-profile.md`
3. Inform user: "Created your master profile at docs/resume/master-profile.md. This will be your base for all future tailoring."

---

## STAGE 1: Intake & Analysis

**Use a subagent** for this stage to protect the main context from heavy parsing.

The subagent receives:
- Raw resume/master profile text
- Raw job description text

The subagent must extract and return:

### From Resume
- List of roles: title, company, dates, key achievements (with metrics), technologies
- Skills inventory
- Education and certifications
- Career narrative arc (how roles connect)

### From Job Description
- Company name and context
- Target role title and seniority level
- Required skills (must-have)
- Preferred skills (nice-to-have)
- Key responsibilities
- Domain/industry
- Implicit requirements (read between the lines)

### Gap Analysis
- **Strong matches:** Resume skills/experience that directly map to JD requirements
- **Partial matches:** Related experience that can be reframed
- **Gaps:** JD requirements not covered by resume
- **Differentiators:** Resume strengths beyond JD requirements (potential value-adds)
- **Red flags:** Date overlaps, unexplained gaps, title inconsistencies

---

## STAGE 1.5: Resume Feedback

**Present the analysis results to the user before asking strategic questions.** This gives the user context for their answers and surfaces issues that may need discussion.

### Format

Present a structured feedback report:

```
## Resume Analysis for [JD Target Role] at [Company]

### Strengths (what works well for this role)
- [Strength 1 — specific, tied to JD requirement]
- [Strength 2]
- [Strength 3]

### Issues to Address
- [Issue 1 — specific problem with actionable suggestion]
- [Issue 2]

### Gap Analysis vs Job Description
| JD Requirement | Your Coverage | Status |
|---|---|---|
| [Required skill/experience] | [Where it appears in resume or "Not found"] | Match / Partial / Gap |

### Red Flags (if any)
- [Date overlaps, unexplained gaps, title inconsistencies — only if present]

### Recommendations
- [Concrete suggestion 1 — e.g., "Your Deeplay role metrics (30% cost reduction) directly map to their 'cost optimization' requirement — should be prominent"]
- [Concrete suggestion 2 — e.g., "Web3 experience at Pontem is a differentiator, not a distraction — reframe as 'distributed systems in adversarial environments'"]
```

### Rules for Feedback
- **Be specific, not generic.** "Your skills section is too broad" is useless. "CRM and Transformers are noise for this JD — cut them, add Kafka and ArgoCD which the JD requires" is actionable.
- **Tie every observation to the JD.** Generic resume advice belongs in references/ats-rules.md, not in per-job feedback.
- **Flag only real issues.** If dates are clean and structure is fine, don't invent problems. No filler critique.
- **Distinguish structural issues from content issues.** Two-column layout is a structural issue (always fix). Missing a specific keyword is a content issue (fix for this JD only).
- **Include the match/gap table.** This is the most actionable artifact — the user sees exactly what's covered and what's not.

### After Presenting Feedback
Say: "This is my analysis of your resume against this specific JD. I'll use this to guide the tailoring. Now let me ask a few strategic questions before I start."

Then proceed to Stage 2.

---

## STAGE 2: Strategic Questions

Ask 3-5 questions via AskUserQuestion. Adapt based on what the analysis reveals.

### Always Ask

**Q1: Locale**
```
"Target locale for this resume?"
Options:
  - English/US (no photo, no DOB, experience before education, 1-2 pages)
  - Russian/CIS (photo, DOB, education before experience, 2-3 pages)
```

**Q2: Positioning**
```
"Based on this JD, I see [brief JD summary]. How should I position you?"
Options:
  - Technical depth (hands-on architecture, system design, specific tech expertise)
  - Leadership breadth (team scaling, org design, strategy, stakeholder management)
  - Balanced (let me decide based on JD emphasis)
```

**Q3: Emphasis**
```
"Your strongest matches for this role are: [top 3-4 from gap analysis].
 Gaps I identified: [top 2-3].
 Which roles or achievements should I highlight most? Anything to de-emphasize?"
Options:
  - Use my recommendations as-is
  - [User provides specific guidance]
```

### Conditional Questions

**Q4: Date Overlaps** (only if detected in analysis)
```
"I see overlapping dates: [Role A: dates] and [Role B: dates]. How should I present this?"
Options:
  - Concurrent roles (consulting + employment)
  - Transition period (brief overlap during handoff)
  - Part-time / advisory capacity
  - [User explains]
```

**Q5: Domain Scope** (only if resume contains domains not in JD)
```
"Your resume includes [domain X] experience. The JD focuses on [domain Y]. How should I handle it?"
Options:
  - Keep with reduced detail (shows breadth)
  - Reframe to highlight transferable skills
  - Minimize (focus entirely on target domain)
```

**Q6: Cover Letter**
```
"Should I generate a cover letter for this application?"
Options:
  - Yes
  - No, resume only
```

### Assemble Tailoring Strategy
After all questions: compile a tailoring strategy document (internal, not saved) capturing:
- Locale choice and its implications
- Positioning direction
- Which roles/achievements to feature
- How to handle gaps and overlaps
- Cover letter decision

---

## STAGE 3: ATS Optimization & Tailoring

Read the appropriate reference files:
- `references/ats-rules.md` (always)
- `references/locale-en.md` OR `references/locale-ru.md` (based on Q1)
- `references/section-templates.md` (always)

### 3.1 Executive Summary
Write specifically for THIS role using the template from section-templates.md.
- Mention the target role title (or close variant)
- Include 1-2 headline metrics from resume that match JD priorities
- Forward-looking statement tied to the target company/role
- ANTI-PATTERN CHECK: Does it start with "Seasoned/Dynamic/Results-driven"? If yes, REWRITE.

### 3.2 Core Skills Section
- Create 3-5 functional categories
- Prioritize categories by JD relevance (most relevant first)
- Each JD required skill must appear here
- Remove skills irrelevant to this role (keep in master profile, not in tailored version)
- Use pipe-delimited format within categories

### 3.3 Professional Experience
- **Recent 2-3 roles:** Full detail using the full-detail template
  - Company context line (what they do, size, domain)
  - Scope statement (team size, budget, mandate)
  - 3-5 achievement bullets with metrics using the achievement formula
- **Older roles:** Condensed using the condensed template
  - 1-2 most JD-relevant achievements only
  - Do NOT eliminate entirely — condense
- **Date overlaps:** Present as decided in Q4
- **Metrics:** Every bullet must have a number. Tie each metric to business context.

### 3.4 Keyword Integration
- For each JD required skill: ensure it appears in at least 2 places:
  1. Core Skills section
  2. An achievement bullet in Professional Experience
- Use semantic variants (not exact repetition) across mentions
- Cap at 3 appearances per keyword (no stuffing)

### 3.5 Education & Certifications
- Apply locale-appropriate ordering (EN: after experience; RU: before experience)
- Certifications relevant to the role should be prominent

### 3.6 Locale-Specific Adjustments
- EN: Strip photo, DOB, marital status. City + state only. LinkedIn URL.
- RU: Include photo placeholder note, DOB, full city. Languages section mandatory.
- Length check: EN max 2 pages (~800 words), RU max 3 pages (~1200 words)

---

## STAGE 4: Draft Generation

### Save Draft
Save to: `docs/resume/draft-[company-slug]-[YYYY-MM-DD].md`

The draft file contains:
1. The full tailored resume in markdown
2. A sentinel marker: `<!-- ATS-ANALYSIS-START -->`
3. ATS Analysis section (for review only, stripped before final output):

```markdown
<!-- ATS-ANALYSIS-START -->

## ATS Analysis (review only — not included in final document)

### Estimated ATS Score: [XX]%

### Keyword Coverage
| JD Requirement | Found In | Section |
|---|---|---|
| [skill/requirement] | Yes/No | [where it appears] |

### Scoring Breakdown
- Required skills coverage: X/Y (Z%)
- Nice-to-have coverage: X/Y (Z%)
- Achievement quantification rate: Z%
- Section completeness: Z%
- Keyword distribution: Z%

### Gaps Remaining
- [requirement not addressed and why]

### Tailoring Decisions Applied
- Locale: [choice]
- Positioning: [choice]
- Emphasized: [what]
- De-emphasized: [what]
- Cover letter: [yes/no]
```

### Present to User
"Here is your tailored resume draft. The ATS analysis section at the bottom shows keyword coverage and scoring — it will not appear in the final document. Please review and let me know what to adjust."

---

## STAGE 5: Review & Iteration

```
LOOP:
  Wait for user feedback
  IF "approved" / "looks good" / "finalize" / "done":
    Proceed to Stage 6
  ELSE:
    Apply requested changes to the draft
    Re-calculate ATS score
    Update the draft file
    Present updated version with changes highlighted
```

---

## STAGE 6: Final Output

### 6.1 Prepare Clean Markdown
Strip the ATS Analysis section (everything after the `<!-- ATS-ANALYSIS-START -->` marker).

### 6.2 Generate DOCX
```bash
# Strip ATS analysis and convert to DOCX
sed '/^<!-- ATS-ANALYSIS-START -->$/,$d' "docs/resume/draft-[slug]-[date].md" > /tmp/resume-clean.md

# Check for custom template
if [ -f "docs/resume/reference.docx" ]; then
  pandoc /tmp/resume-clean.md -o "docs/resume/[Name]-[TargetRole]-resume.docx" \
    --from markdown \
    --reference-doc="docs/resume/reference.docx"
else
  pandoc /tmp/resume-clean.md -o "docs/resume/[Name]-[TargetRole]-resume.docx" \
    --from markdown \
    -V geometry:margin=0.75in \
    -V fontsize=11pt
fi
```

### 6.3 Cover Letter (if requested)
Generate using the cover letter template from `references/section-templates.md`:
- Save markdown: `docs/resume/cover-letter-[slug]-[date].md`
- Locale-appropriate tone and structure
- Convert to DOCX:
```bash
if [ -f "docs/resume/reference.docx" ]; then
  pandoc "docs/resume/cover-letter-[slug]-[date].md" -o "docs/resume/cover-letter-[slug]-[date].docx" \
    --from markdown \
    --reference-doc="docs/resume/reference.docx"
else
  pandoc "docs/resume/cover-letter-[slug]-[date].md" -o "docs/resume/cover-letter-[slug]-[date].docx" \
    --from markdown \
    -V geometry:margin=0.75in \
    -V fontsize=11pt
fi
```

### 6.4 Report
List all generated files:
```
Generated files:
- Resume (markdown): docs/resume/draft-[slug]-[date].md
- Resume (DOCX): docs/resume/[Name]-[TargetRole]-resume.docx
- Cover letter (markdown): docs/resume/cover-letter-[slug]-[date].md  [if applicable]
- Cover letter (DOCX): docs/resume/cover-letter-[slug]-[date].docx  [if applicable]
```

### 6.5 Master Profile Update
"Would you like me to update your master profile with any new information from this session?"

---

## Pre-Output Verification Checklist

Run this checklist before presenting the draft in Stage 4. Every item must pass.

### ATS Compliance
- [ ] Single column layout (no markdown tables for layout)
- [ ] Contact info in document body (not relying on header rendering)
- [ ] Standard section headings used (per ats-rules.md)
- [ ] Consistent date format throughout
- [ ] No skill bars, icons, or visual ratings
- [ ] Acronyms spelled out on first use

### Keyword Coverage
- [ ] Every required JD skill appears in 2+ places (skills section + experience bullet)
- [ ] No keyword appears more than 3 times total
- [ ] Job title from JD reflected in Executive Summary
- [ ] Semantic variants used across mentions (not exact repetition)

### Content Quality
- [ ] Executive Summary is specific to THIS role (not generic/templated)
- [ ] Every achievement bullet contains a quantified metric
- [ ] Metrics include business context (not floating numbers)
- [ ] Recent 2-3 roles have full detail
- [ ] Older roles are condensed but not eliminated
- [ ] No unexplained gaps > 6 months (flagged to user)
- [ ] Date overlaps explained per user's direction
- [ ] Domain breadth preserved (not over-narrowed to JD domain)

### Locale Compliance
- [ ] EN: No photo, no DOB, no marital status, experience before education
- [ ] RU: Photo noted, DOB included, education before experience
- [ ] Length within locale norms (EN: ~800 words / 2 pages; RU: ~1200 words / 3 pages)
- [ ] Tone matches locale expectations

### Anti-Pattern Check
- [ ] Executive Summary does NOT start with "Seasoned/Dynamic/Results-driven/Passionate"
- [ ] No "References available upon request"
- [ ] No skill percentage/proficiency ratings
- [ ] Early career NOT aggressively condensed to nothing
- [ ] Adjacent domain experience NOT stripped (reframed instead if needed)
- [ ] No responsibilities-only descriptions without achievements
