# ATS Rules & Scoring Heuristics

## Format Rules

### Layout
- Single column ONLY. No multi-column, no tables for layout, no text boxes
- No graphics, icons, charts, progress bars for skill levels
- Contact info in document body (NOT in header/footer — Workday and others skip headers entirely)
- No "skill bars" or percentage ratings — ATS cannot parse visual skill levels
- Photos can disrupt ATS parsing (shift text positions, break field extraction). For ATS submissions — no photo. Add photo only for direct submissions in EU/CIS markets where expected
- No special characters (★ ● ◆) — use standard bullets or dashes
- No em dashes in dates — use hyphens

### Section Headings
Use these exact standard headings (ATS systems look for them):
- EN-CA: "Profile Summary", "Technical Skills", "Work Experience", "Education", "Certifications", "Professional References"
- FR-CA: "Résumé du Profil", "Compétences Techniques", "Experience de Travail", "Éducation", "Certifications", "Références Professionnelles"
- EN: "Executive Summary", "Core Skills", "Professional Experience", "Education", "Certifications"
- RU: "Краткое описание", "Ключевые навыки", "Опыт работы", "Образование", "Сертификации"
- Creative alternatives ("My Journey", "What I Bring") break ATS section detection

### File Format (updated 2026)
- `.pdf` (text-based) — now parses as well as DOCX across Greenhouse, Lever, Workday, iCIMS. Default choice — preserves formatting perfectly
- `.docx` — still has 23% fewer parsing errors with design tools (Workday data); use when portal explicitly requests it or legacy ATS suspected
- HTML-to-PDF (weasyprint, Puppeteer, Chrome print) — produces clean text-based PDFs that parse correctly
- **Test:** if you can highlight and copy text in the PDF, ATS can read it
- Never: `.pages`, `.odt`, Google Docs links, scanned/image-based PDFs

### Typography
- Fonts:  Arial
- Full name (header): 16 pt, bold, centered
- Section titles: 12 pt, bold, color #205968, uppercase, underlined with a black bottom border (1pt)
- Everything else: 11 pt, normal (job titles, dates, and skill labels are bold at 11 pt, but remain size 11 pt)
- Margins: 0.5-1 inch (0.75 inch. Not recommanded for Canada resume)
- No decorative fonts or unusual typefaces
- Document Format (Page Size) for Canada: Format= US Letter (Lettre É.-U.). Dimension= 21.59 cm x 27.94 cm (8.5 in x 11 in).his is the standard professional format for North American (Canada/US) resumes
- Document Margins for Canada: Top= 1.04 cm, Bottom= 1.88 cm, Left= 2.54 cm, Right=2.54 cm, Gutter= 0 cm. 

### Dates
- Consistent format throughout the entire document
- Preferred: YYYY
- Never mix formats (e.g., "Jan 2024" in one place and "01/2024" in another)

### Acronyms
- Spell out on first use with acronym in parentheses
- Example: "Application Programming Interface (API)"
- After first use, acronym only is fine

---

## Keyword Matching (How Modern ATS Works)

### Semantic NLP Matching (2025-2026)
Modern ATS (Greenhouse, Lever, Workday) uses semantic understanding — 91% accuracy vs 67% for keyword-only systems:
- Greenhouse: semantic matching; "Managed teams" ≈ "Led teams"
- Workday: NLP-based; maps "project management" → "program coordination" → "initiative leadership"
- Lever: semantic matching with AI workflows
- iCIMS: more traditional keyword matching with AI screening add-ons
- Exact match is still strongest signal, but synonyms and related terms count
- **Tailored resumes show 115% higher conversion rate** (6.5% vs 2.68% for generic). Job title alignment gives ~3.5x increase in interview rates

### Keyword Placement Strategy
- Every required JD skill must appear in at least 2 places:
  1. Technical / Core Skills section (grouped by category)
  2. An achievement bullet in Professional Experience
- Nice-to-have skills: at least 1 mention somewhere
- Use semantic variants across mentions (not exact repetition)

### Keyword Density
- Each keyword: 2-3 appearances max across the whole document
- More than 3 = keyword stuffing, detected and penalized
- Job title from JD should be reflected in Executive Summary

### What NOT to Do
- Never create an invisible/white-text keyword block — ManpowerGroup detects hidden text in ~100K resumes/year; Greenhouse found 1% of all resumes contain white text. ATS strips formatting on profile pages, exposing hidden text to recruiters
- Never use prompt injection / hidden AI instructions in resume — 41% of US and Canadian job seekers tried this; companies actively build detection
- Never list skills without contextual demonstration in experience
- Never repeat the same phrase verbatim more than twice

---

## ATS Scoring Formula

Estimated score for pre-output analysis:

```
Score = 0.4 * required_skills_coverage
      + 0.2 * nice_to_have_coverage
      + 0.2 * achievement_quantification_rate
      + 0.1 * section_completeness
      + 0.1 * keyword_distribution
```

### Component Definitions

**required_skills_coverage:** Count of JD "required" / "must-have" skills found in resume / total required skills

**nice_to_have_coverage:** Count of JD "preferred" / "nice-to-have" skills found / total nice-to-have skills

**achievement_quantification_rate:** Percentage of experience bullets containing a numeric metric (dollar amount, percentage, team size, time reduction, scale number)

**section_completeness:** All standard sections present and non-empty (Contact, Summary, Skills, Experience, Education). Score = present sections / 5

**keyword_distribution:** Keywords appear across multiple sections (not concentrated in one). Score = sections containing JD keywords / total sections with content

### Score Interpretation
- 85%+ : Strong match, likely to pass ATS filters
- 70-84%: Moderate match, may pass depending on competition
- Below 70%: Weak match, significant gaps to address

---

## Anti-Patterns to Detect and Flag

### Executive Summary
- NEVER start with: "Seasoned", "Dynamic", "Results-driven", "Passionate", "Highly motivated"
- NEVER use: "[Title] with N+ years of experience in [broad field]" as opening
- MUST be specific to the target role, mentioning the actual job title and 1-2 headline metrics

### Profile Summary / Résumé du Profil
- NEVER generate a block paragraph layout; the summary must strictly be formatted as a bulleted list containing 4 to 8 distinct points
- NEVER start with generic filler words or clichés: "Seasoned", "Dynamic", "Results-driven", "Passionate", "Highly motivated"
- NEVER use broad opening hooks such as "[Title] with N+ years of experience in [broad field]"
- MUST align directly with the targeted Canadian role, referencing the specific job title, 1–2 quantifiable headline metrics, and forward-looking business intent

### Work Experience / Expérience de Travail
- For Canadian resume, you can use the word "Present" or an ellipsis ("…") for an active, ongoing role, if applicable;
- NEVER start achievement bullets with weak or passive phrases: "Responsible for", "Tasked with", "Assisted with", "Worked on", "Participated in", "Was involved in"
- NEVER write achievement bullets without metrics; every bullet point must incorporate a quantified business outcome
- NEVER present historical roles (older than 10 years) with full programmatic detail; these positions must be tightly condensed down to title/date on a single line and 2–8 highly relevant achievements

### Personal Information & Compliance for Canadian resume / Renseignements personnels pour un CV canadien
- NEVER include a profile photo or headshot placeholder
- NEVER display a full street or mailing address; limit the location strictly to "City, Province" (e.g., "Gatineau, QC", "Moncton, NB")
- NEVER include protected discrimination vectors: date of birth, age, marital/civil status, religion, health status, or Social Insurance Number (SIN)

### Technical Skills & Layout Structure for canadian resume
- NEVER populate a functional skills category with fewer than 4 or more than 6 individual skills
- NEVER mix parsing symbols or styling choices; skills must be cleanly separated using vertical pipes (`|`) or commas consistently within categories
- NEVER implement complex graphic layouts, text boxes, or multi-column grids that inhibit Applicant Tracking Systems (ATS)
- NEVER let the final document footprint leak past a strict maximum ceiling of 2 pages
- NEVER mix regional telephone formats; the layout must strictly match `XXX-XXX-XXXX` for EN-CA profiles or `(XXX) XXX-XXXX` for FR-CA profiles

### Content
- Metrics without business context: "Increased revenue 30%" — of what baseline? what division?
- Responsibilities-only descriptions without achievement bullets
- Role descriptions that could apply to anyone in that title (not specific to this person)
- Unexplained gaps > 6 months (flag to user, don't auto-fill or hide)

### Structure
- "References available upon request" — remove entirely (outdated convention)
- Objective statements (replaced by Executive Summary or Profile Summary)
- Skill percentage ratings or proficiency bars
- Aggregated achievement blocks detached from specific roles/periods

### Tailoring
- Over-narrowing: stripping relevant domain experience to match JD literally
- Under-tailoring: generic resume not customized for this specific role
- Early career elimination: condensing significant roles to nothing just because they're old
- Ignoring transferable skills from adjacent domains