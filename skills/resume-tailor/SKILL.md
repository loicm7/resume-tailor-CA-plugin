---
name: resume-tailor
description: |
  Tailors a resume to a specific job description with ATS optimization.
  Use when asked to optimize a resume, tailor a CV for a job (Canada, US,), create an ATS-friendly resume,
  generate a cover letter, or adapt a career profile for a specific regional role.
  Triggers on: "resume", "CV", "tailor", "ATS", "job application", "cover letter",
  "optimize resume", "adapt resume", "curriculum vitae", "bilingue", "optimise CV", "adapte CV", "lettre de motivation", "offre emploi", "резюме", "вакансия".
user-invocable: true
---

# Resume Tailor

Multi-stage skill that transforms a resume into an ATS-optimized document tailored to a specific job description. Supports Canadian English (EN-CA) and Canadian French (FR-CA), EN/US and RU/CIS locales. Replaces generic AI resume services with job-specific, strategic tailoring.

## Reference Files

Read these files at the appropriate stage — they contain rules, conventions, and templates:
- `references/ats-rules.md` — ATS formatting rules, keyword strategy, scoring formula, anti-patterns
- `references/locale-en-ca.md` — Canadian English resume conventions
- `references/locale-fr-ca.md` — Canadian French resume conventions
- `references/section-templates-ca.md` — Section templates with slot markers for both Canadian locales
- `references/locale-en.md` — English/US resume conventions
- `references/locale-ru.md` — Russian/CIS resume conventions
- `references/section-templates.md` — Section templates with slot markers for English/US and Russian/CIS locales
- `references/template-en-ca.html` — EN-CA HTML template for weasyprint PDF (no photo, centered header, 190px skills column grid, ellipsis or present ongoing date format)
- `references/template-ca.html` — FR-CA HTML template for weasyprint PDF (no photo, centered header, 200px skills column grid, ellipsis or present ongoing date format)
- `references/html-template-en.html` — EN HTML template for weasyprint PDF (no photo, centered header, 170px skills grid)
- `references/html-template-ru.html` — RU HTML template for weasyprint PDF (photo, flexbox header, 135px/8.5pt skills grid)

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
## Visibility Schema (enum definitions and known variants)
## Roles (each with: Visibility, Variants, company context, title, dates, team size/structure, responsibilities, achievements with metrics, technologies)
## Education (with Visibility)
## Certifications
## Skills Inventory (comprehensive, not pre-filtered)
## Languages
## Notes (gaps, side projects, consulting arrangements, etc.)
```

### Visibility System

The master profile may include a `Visibility` field on each role and section. If the master profile has no `Visibility` fields, treat all roles as `always`.

| Value | Meaning | Behavior |
|---|---|---|
| `always` | Include in all resume variants | Always consider for inclusion |
| `variant-specific` | Only in variants listed in `Variants` field | Include only if target variant matches or user explicitly requests |
| `on-request` | Never include unless user explicitly asks | Skip by default |
| `reference-only` | Alias/metadata, not content | Never include in any output |

Variants are defined in the master profile's Visibility Schema section. Read them from there — do not hardcode.

### Visibility Resolution Rules

1. **Determine target variant.** Infer from user's request, JD domain, or ask if ambiguous. User may also specify constraints like "without variant X" — treat as explicit variant exclusion.
2. **Filter roles:**
   - `always` → include
   - `variant-specific` → include only if target variant is in `Variants` list AND not explicitly excluded by user
   - `on-request` → skip unless user explicitly names the role or asks for a full/comprehensive resume
   - `reference-only` → never include
3. **Education, Certifications, Languages** — respect their `Visibility` the same way.
4. **User overrides win.** If user explicitly asks to include an `on-request` role — include it. If user excludes a variant — drop all roles where that variant is the only match.

---

## Resume Optimization

### 1. Vocabulary Strategy
I intentionally avoid the "LLM-blacklist" that flags AI-written content. 
*   **Forbidden:** Pioneered, revolutionized, spearheaded, orchestrated, dynamic, transformative, foster, leverage, dive/delve, fast-paced, synergy, results-driven.
*   **Preferred:** Led, built, managed, designed, increased, cut, improved, identified, resolved.

### 2. Realistic Metric Reporting
Clean, round numbers (e.g., 50%) often signal automated filler. To maintain authenticity:
*   **Asymmetrical Data:** I use uneven figures (e.g., 14%, 32%, 7%) to mimic actual, messy business reporting.
*   **Precision:** If a metric is an estimate, it will reflect the nuance of real-world tracking rather than marketing inflation.

### 3. Contextual Keyword Anchoring
Keywords must be grounded to be effective. I avoid floating buzzwords by tethering them directly to your hard skills or tools.
*   **Example:** Instead of just "optimized project management," I write, "Managed Jira tickets for the Q3 migration, which reduced developer backlog by 12%."

### 4. Syntax and Rhythm
To avoid the repetitive cadence of AI-generated bullet points, I vary the sentence structure:
*   **Punchy:** "Automated server deployment scripts using Python."
*   **Compound:** "Redesigned the internal inventory dashboard, which cut manual data entry time by 22% and allowed the team to focus on client outreach."

### 5. The "Coffee-Shop" Test
If a sentence sounds like it was lifted from a corporate brochure, it is rewritten. I aim for the tone of a professional explaining their work to a peer. 
*   **Corporate:** "Fostered a synergy-driven environment to leverage cross-functional collaboration."
*   **Human/Professional:** "Coordinated with the design and engineering teams to push the new build through testing, ensuring we hit the launch deadline despite the API instability."

---

## Resume Strategy: Authentic Writing vs. AI Optimization

### Part 1: Red Flags of AI-Generated Resumes
These signals tell a recruiter that the document was likely generated by a model, causing them to question your actual experience:

* **The "Adjective Bloat":** Excessive use of fluff like *“visionary,” “passionate,” “results-driven,”* or *“dynamic.”*
    * *Fix:* Replace these with hard evidence. Instead of "results-driven developer," use "5+ years of experience delivering React/ASP.NET applications."
* **Uniform Sentence Structure:** Every bullet starts with a "power verb" (Spearheaded, Orchestrated, Optimized) and ends with a generic impact statement.
    * *Fix:* Vary your cadence. Use short, punchy results mixed with longer, narrative-style descriptions of the "how" and "why."
* **The "Mirror" Effect:** Using the exact adjectives from the job description (e.g., if they ask for a "collaborative, self-motivated, detail-oriented" person, the profile summary says exactly that).
    * *Fix:* Interpret the requirements. If they want "collaboration," write: "Coordinated cross-functional workflows between design and backend teams to reduce sprint delays."
* **Hallucinated Metrics:** Numbers that feel "tidy" (e.g., "Increased efficiency by 50% across the board"). These often feel disconnected from reality.
    * *Fix:* Use specific, slightly irregular numbers that feel real (e.g., "Reduced server response time by 18% over 3 months").

### Part 2: How to Pass ATS (Without "Stuffing")
ATS systems aren't just looking for words; they are looking for **standardized data structures**.

1. Format for the Machine:
- **Single Column:** Avoid tables, text boxes, and graphic elements.
- **Standard Headings:** Use "Experience," "Education," "Skills," and "Certifications." Do not try to be creative (e.g., avoid "My Journey" or "Core Competencies").
- **Standard Fonts:** Use Arial, Calibri, or Helvetica.
- **File Type:** Use a clean, text-based PDF or .docx.

2. Contextual Keyword Integration:
- Mirror the **terminology** (the specific tools/technologies, like "Kubernetes" vs. "k8s"), but **not the phrasing** of the soft skills.
- Place keywords in your "Skills" section and organically within your bullet points. A keyword is most powerful when it’s linked to an outcome (e.g., "Used **React** to refactor legacy code, improving load speed by 20%").

### Part 3: How to Prompt AI for a "Human" Result
If I ask you to "write my resume.", use **Constraint-Based Prompting** to sound like me.

- Suggested Prompt Template:
> "I am providing my raw experience and a target job description. Rewrite my experience section using these specific constraints:
> 1. **Voice:** Professional, direct, and slightly conversational. No 'corporate-speak' or fluff words (e.g., no 'visionary,' 'passionate,' 'synergy').
> 2. **Structure:** Use the 'Action + Context + Result' formula. If a bullet doesn't have a concrete outcome or tool, either remove it or ask me to provide a metric.
> 3. **Keyword Strategy:** Incorporate the specific technologies from the job description naturally into the bullet points. Do not 'stuff' them.
> 4. **Humanize:** Use varied sentence lengths. Some bullets should be short and punchy; others can describe a brief 'how' or 'why' behind the work.
> 5. **Tone:** Write like a high-performing peer describing their work to a colleague in an interview, not like a marketing brochure."

---

## Design and Layout
Use `references/template-en-ca.html` or `references/template-ca.html` based on EN-CA and FR-CA as a layout and style model for the final output Word DOCX file you will create. I want the Word document layout and style you create looks exactly like those html files or the Word document I attached. Do not reduce font size keep the same size and if your text does not fit the 2 pages max, reformulate or make try to make short text.

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
- [Concrete suggestion 1 — e.g., "Your Role X metrics (30% cost reduction) directly map to their 'cost optimization' requirement — should be prominent"]
- [Concrete suggestion 2 — e.g., "Adjacent domain experience at Company Y is a differentiator, not a distraction — reframe as 'distributed systems in adversarial environments'"]
```

### Rules for Feedback
- **Be specific, not generic.** "Your skills section is too broad" is useless. "CRM and Transformers are noise for this JD — cut them, add Kafka and ArgoCD which the JD requires" is actionable.
- **Tie every observation to the JD.** Generic resume advice belongs in references/ats-rules.md, not in per-job feedback.
- **Flag only real issues.** If dates are clean and structure is fine, don't invent problems. No filler critique.
- **Distinguish structural issues from content issues.** Two-column layout is a structural issue (always fix). Missing a specific keyword is a content issue (fix for this JD only).
- **Include the match/gap table.** This is the most actionable artifact — the user sees exactly what's covered and what's not.

### Excluded Roles Report

After the main feedback, present a separate block listing every role and section excluded from this resume and why:

```
### Excluded from This Resume
| Role / Section | Visibility | Reason Excluded |
|---|---|---|
| [Role name] | variant-specific | Variant "X" does not match target variant "Y" |
| [Role name] | variant-specific | Variant "X" explicitly excluded by user |
| [Role name] | on-request | Not explicitly requested |
| [Section name] | on-request | Not explicitly requested |
```

Rules for this report:
- List ALL excluded roles, not just some. The user must see the full picture.
- State the exact reason: visibility value, variant mismatch, user exclusion, etc.
- If a role is excluded because the user said "without X" — cite the user's constraint verbatim.
- `always` roles are never listed here (they're always included).
- `reference-only` entries are never listed here (they're metadata, not roles).
- If the master profile has no Visibility fields, skip this report entirely.

### After Presenting Feedback
Say: "This is my analysis of your resume against this specific JD. Above is the list of roles I'm excluding and why. If you want to include or exclude anything differently, let me know. Now let me ask a few strategic questions before I start."

Then proceed to Stage 2.

---

## STAGE 2: Strategic Questions

Ask 3-5 questions via AskUserQuestion. Adapt based on what the analysis reveals.

### Always Ask

**Q1: Locale**
```
"Target locale for this resume?"
Options:
  - Canadian English (EN-CA: No photo, no DOB, city + province only, phone format XXX-XXX-XXXX, max 2 pages)
  - Canadian French (FR-CA: No photo, no DOB, city + province only, phone format (XXX) XXX-XXXX, max 2 pages)
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
- `references/locale-en-ca.md` OR `references/locale-fr-ca.md` OR `references/locale-en.md` OR `references/locale-ru.md` (based on Q1)
- `references/section-templates-ca.md` OR `references/section-templates.md` (always, based on Q1)


### 3.1 Profil Summary (use this section for Canada only)
Write specifically for THIS role using the bulleted list layout from `section-templates-ca.md`[cite: 5].
- Must be a bulleted list containing 4 to 8 distinct points (no block paragraphs).
- Mention targeted Canadian role alignment, primary technical/business domain, 1-2 headline metrics, and forward-looking intent
- Formatting: Normal 11pt text with no excessive bolding on whole bullets.
- ANTI-PATTERN CHECK: Does it start with "Seasoned/Dynamic/Results-driven/Passionate"? If yes, REWRITE

### 3.2 Executive Summary (use this section for others)
Write specifically for THIS role using the template from section-templates.md.
- Mention the target role title (or close variant)
- Include 1-2 headline metrics from resume that match JD priorities
- Forward-looking statement tied to the target company/role
- ANTI-PATTERN CHECK: Does it start with "Seasoned/Dynamic/Results-driven"? If yes, REWRITE.

### 3.2 Technical Skills Section (use this section for Canada only)
- Create 3-6 functional categories aligned with terms in the Job Description.
- Each category must contain 4-6 skills, separated cleanly by pipes (`|`) or commas.
- Remove skills irrelevant to this role (keep in master profile, not in tailored version)
- Prioritize categories by JD relevance (most critical category first).
- All key JD-required skills must appear here. Maintain complete technological consistency

### 3.3 Core Skills Section (use this section for others)
- Create 3-5 functional categories
- Prioritize categories by JD relevance (most relevant first)
- Each JD required skill must appear here
- Remove skills irrelevant to this role (keep in master profile, not in tailored version)
- Use pipe-delimited format within categories

### 3.4 Work Experience (use this section for Canada only)
- Always follow reverse-chronological order[cite: 1, 2].
- **Recent roles (Past 10 years / 2-4 positions):** Full detail using the full-detail template
  - Job title and dates on the top line; Employer name, City, and Province underneath.
  - Active/Ongoing roles must use an ellipsis ("…") for the end date  or "Present".
  - Focused 1-2 sentence overview defining the underlying scope or mandate
  - 3-5 achievement bullets backed by quantified metrics using the Canadian Achievement Formula
- **Older roles (Beyond 10 years):** Condensed using the condensed template
  - Title and date on the same line, with the company on the line below
  - 2-8 most relevant, metric-backed achievements only. Do not eliminate entirely
- **Metrics:** Every single bullet must contain a context-driven quantified number

### 3.5 Professional Experience (use this section for others)
- **Recent 2-3 roles:** Full detail using the full-detail template
  - Company context line (what they do, size, domain)
  - Scope statement (team size, budget, mandate)
  - 3-5 achievement bullets with metrics using the achievement formula
- **Older roles:** Condensed using the condensed template
  - 1-2 most JD-relevant achievements only
  - Do NOT eliminate entirely — condense
- **Date overlaps:** Present as decided in Q4
- **Metrics:** Every bullet must have a number. Tie each metric to business context.

### 3.6 Keyword Integration
- For each JD required skill: ensure it appears in at least 2 places:
  1. Technical Skills / Core Skills section
  2. An achievement bullet in Work Experience / Professional Experience
- Use semantic variants or localized tracks cleanly (maintain strict spelling Track consistency across terms)
- Cap at 3 appearances per keyword (no keyword-stuffing anti-patterns).

### 3.7 Education & Certifications
- Positioned strictly **after** the Work or Professional experience section (unless a recent graduate within 1-2 years).
- Format: Credential earned (e.g., Bachelor of Science, Diploma of College Studies / DEC), institution, city/province, and graduation year
- Guardrails: No GPA unless explicitly requested by public sector/academic paths. Strip high school if post-secondary is completed unless explicitly requested
- Check master profile — education may be omitted per user preference
- If included: apply locale-appropriate ordering (FR-CA: after experience;EN-CA: after experience; EN: after experience; RU: before experience)
- Certifications relevant to the role should be prominent

### 3.8 Locale-Specific Adjustments
- FR-CA and EN-CA: Strictly strip photo, DOB, age, gender, marital status, religion, SIN, or health parameters. Full name, clean telephone number, professional email, LinkedIn/GitHub URLs if available. Location must be City + Province only.
- EN: Strip photo, DOB, marital status. City + state only. LinkedIn URL.
- RU: Include photo placeholder note, DOB, full city. Languages section mandatory.
- Length check: FR-CA and EN-CA max 2 pages (~790 words), EN max 2 pages (~800 words), RU max 3 pages (~1200 words)

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

### 6.2 Generate PDF via HTML + weasyprint

**Why not pandoc?** Pandoc with xelatex ignores CSS styling and renders photos at full page size. Pandoc with wkhtmltopdf requires a separate binary. weasyprint renders HTML+CSS faithfully — photo sizing, flexbox layout, colors, fonts all work correctly.

**Step 1: Create styled HTML**

Save to `docs/resume/resume-[slug].html`. Use the appropriate HTML template as a starting point:
- EN-CA locale: `references/template-en-ca.html` (centered header, no photo, 190px skills grid, phone: XXX-XXX-XXXX, ellipsis or present ongoing date format)
- FR-CA locale: `references/template-ca.html` (centered header, no photo, 200px skills grid, phone: (XXX) XXX-XXXX, ellipsis or present ongoing date format)
- EN locale: `references/html-template-en.html` (centered header, no photo, 170px skills grid, 9pt skills)
- RU locale: `references/html-template-ru.html` (flexbox header with photo, 135px skills grid, 8.5pt skills)

Replace `[SLOT_MARKERS]` with actual content. Key design rules:
- `break-inside: avoid` on `.role` and `.condensed` prevents page-split mid-role
- `break-inside: avoid` on job roles and education entries prevents page-split midway.
- Maintain Arial or standard North American web-safe font stacks at uniform sizes (16pt for name, 12pt uppercase colored headings with 1pt black bottom border, 11pt normal body).

**Step 2: Convert HTML to PDF**

```bash
cd docs/resume && DYLD_LIBRARY_PATH="$(brew --prefix)/lib" uv run weasyprint resume-[slug].html [Name]-[TargetRole].pdf
```

Requires `weasyprint` (already in project deps via `uv add weasyprint`).
System deps (`glib`, `pango`, `cairo`) are installed via brew.
`DYLD_LIBRARY_PATH` is needed on macOS for weasyprint to find gobject.

**Target page counts:**
- EN-CA and FR-CA locale: Maximum of 2 pages for both locale. If a minor spillover occurs, tighten line-height, margins, and padding rules until it perfectly snaps into a crisp 2-page grid layout.
- EN locale: 2 pages max
- RU locale: 3 pages max

### 6.3 Cover Letter (if requested)
Generate using the cover letter template from `references/section-templates.md`:
- Save HTML: `docs/resume/cover-letter-[slug]-[date].html`
- Locale-appropriate tone and structure
- Convert to PDF with same weasyprint command

### 6.4 Report
List all generated files:
```
Generated files:
- Resume (markdown draft): docs/resume/draft-[slug]-[date].md
- Resume (HTML source):    docs/resume/resume-[slug].html
- Resume (PDF):            docs/resume/[Name]-[TargetRole].pdf
- Cover letter (HTML):     docs/resume/cover-letter-[slug]-[date].html  [if applicable]
- Cover letter (PDF):      docs/resume/cover-letter-[slug]-[date].pdf   [if applicable]
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
- [ ] No keyword appears more than 3 times total (no stuffing markers)
- [ ] Targeted role title cleanly integrated into the Profile Summary
- [ ] Job title from JD reflected in Executive Summary

### Content Quality
- [ ] Profile Summary is a bulleted list of 4 to 8 points (not a paragraph block)
- [ ] Executive Summary is specific to THIS role (not generic/templated)
- [ ] Every achievement bullet contains a quantified metric and starts with a strong action verb and includes a context-driven quantified metric
- [ ] Metrics include business context (not floating numbers)
- [ ] Older roles are condensed but not eliminated
- [ ] No unexplained gaps > 6 months (flagged to user)
- [ ] Date overlaps explained per user's direction
- [ ] Domain breadth preserved (not over-narrowed to JD domain)

### Locale Compliance
- [ ] Absolutely NO photos, dates of birth, age, marital status, or SIN numbers
- [ ] EN-CA and FR-CA: Location is strictly City, Province only
- [ ] Phone layout matches track specification exactly (XXX-XXX-XXXX for EN-CA or (XXX) XXX-XXXX for FR-CA)
- [ ] EN-CA and FR-CA: Education placed after work experience block
- [ ] EN: No photo, no DOB, no marital status, experience before education
- [ ] RU: Photo included, education per master profile preference
- [ ] Tone matches locale expectations
- [ ] Final output prints to standard North American US Letter dimensions and limits strictly to a 2-page ceiling

### Anti-Pattern Check
- [ ] Profile or Executive Summary does NOT start with "Seasoned/Dynamic/Results-driven/Passionate"
- [ ] No "References available upon request"
- [ ] No skill percentage/proficiency ratings
- [ ] Early career NOT aggressively condensed to nothing
- [ ] Adjacent domain experience NOT stripped (reframed instead if needed)
- [ ] No responsibilities-only descriptions without achievements