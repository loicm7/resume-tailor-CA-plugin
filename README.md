# Resume Tailor — Claude Code Plugin

ATS-optimized resume tailoring that replaces generic AI resume services. Instead of a one-size-fits-all rewrite, this skill tailors your resume **per job description** — matching keywords, adjusting emphasis, and structuring content for both human recruiters and ATS systems.

## What It Does

| Stage | What Happens |
|-------|-------------|
| **Intake** | Reads your resume (PDF/text) + job description (text or URL) |
| **Analysis** | Parses both documents, produces gap analysis: matches, gaps, differentiators |
| **Feedback** | Shows you a structured resume review: strengths, issues, gap table vs the JD |
| **Strategic Questions** | Asks 3-5 targeted questions: locale, positioning, emphasis, domain scope |
| **ATS Optimization** | Rewrites for the specific role: tailored summary, grouped skills, keyword placement |
| **Draft + Scoring** | Generates markdown draft with ATS score estimate and keyword coverage table |
| **Final Output** | Produces `.docx` via pandoc, optionally generates a cover letter |

### Why Not Just Use an AI Resume Service?

AI resume services apply the same transformation to every resume:
- Generic executive summary ("Seasoned leader with 15+ years...")
- Over-narrow domain focus (strips adjacent experience)
- No job-specific keyword targeting
- Template formatting without strategic thinking

This skill asks **what the specific job needs** before touching your content.

## Features

- **Job-description-driven** — every tailoring decision is based on the target JD
- **Resume feedback** — structured analysis with strengths, issues, and gap table before tailoring
- **ATS scoring** — estimated match score with keyword coverage breakdown
- **Locale support** — EN/US and RU/CIS conventions (section order, personal info, tone)
- **Master profile** — stores your full career data, never loses context when narrowing
- **Anti-pattern protection** — blocks generic openers, keyword stuffing, over-condensation
- **Cover letter** — optional, also tailored to the specific role
- **DOCX output** — ATS-friendly single-column document via pandoc

## Installation

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed
- [pandoc](https://pandoc.org/installing.html) installed (for DOCX generation)

Verify pandoc:
```bash
pandoc --version
```

### Option 1: Install from GitHub (Recommended)

```bash
# Add the marketplace (first time only)
claude /plugin marketplace add olegvg/resume-tailor-plugin

# Install the plugin
claude /plugin install resume-tailor
```

### Option 2: Clone and Install Locally

```bash
# Clone the repository
git clone https://github.com/olegvg/resume-tailor-plugin.git

# Install from local path
claude /plugin install --path ./resume-tailor-plugin
```

### Option 3: Manual Installation

Copy the plugin into your Claude Code plugins directory:

```bash
# Clone
git clone https://github.com/olegvg/resume-tailor-plugin.git

# Copy to Claude Code plugins directory
cp -r resume-tailor-plugin ~/.claude/plugins/resume-tailor-plugin
```

After manual installation, restart Claude Code for the skill to be detected.

## Usage

### Basic: Resume + Job Description

```
/resume-tailor

Provide:
- Resume: path/to/your-resume.pdf
- Job: https://linkedin.com/jobs/view/123456
```

Or paste the job description directly:

```
/resume-tailor

Resume: ~/Documents/my-resume.pdf

Job Description:
We are looking for a Senior Backend Engineer...
[paste full JD text]
```

### With Master Profile

On first run, the skill creates a **master profile** — a comprehensive data store of your career at `docs/resume/master-profile.md`. Subsequent runs use this profile as the base:

```
/resume-tailor

Job: https://greenhouse.io/job/senior-platform-engineer-at-stripe
```

The skill will ask: "Using your master profile as the base. Correct?"

### What to Expect

1. **Analysis phase** — skill parses your resume and JD, identifies matches and gaps
2. **Feedback** — structured resume review: strengths for this role, issues to address, gap table showing every JD requirement and where it appears (or doesn't) in your resume
3. **Questions** — 3-5 strategic questions (locale, positioning, emphasis)
4. **Draft** — markdown resume with ATS analysis section showing:
   - Estimated ATS score (%)
   - Keyword coverage table (which JD requirements appear where)
   - Gaps remaining
5. **Review** — you provide feedback, skill iterates
6. **Final output** — `.docx` file ready to submit

### Output Files

```
docs/resume/
  master-profile.md                    # Your career data (created once)
  draft-stripe-2026-03-16.md           # Markdown draft with ATS analysis
  John-Doe-Senior-Engineer-resume.docx # Final DOCX
  cover-letter-stripe-2026-03-16.md    # Cover letter (if requested)
  cover-letter-stripe-2026-03-16.docx  # Cover letter DOCX
```

### Custom DOCX Template

For branded styling, place a Word template at `docs/resume/reference.docx`. The skill will use it for consistent fonts, colors, and spacing:

```bash
# The skill detects and uses it automatically
docs/resume/reference.docx
```

## Using with Claude Desktop

Claude Desktop does not support plugins directly, but you can use this skill through **Projects with custom instructions**.

### Setup

1. Open **Claude Desktop** → **Projects** → **Create New Project**
2. Name it "Resume Tailor" (or any name you prefer)
3. Click **Set custom instructions**
4. Paste the full content of `skills/resume-tailor/SKILL.md` into the instructions field
5. Upload the reference files as project knowledge:
   - `skills/resume-tailor/references/ats-rules.md`
   - `skills/resume-tailor/references/locale-en.md`
   - `skills/resume-tailor/references/locale-ru.md`
   - `skills/resume-tailor/references/section-templates.md`

### Usage in Claude Desktop

1. Open the "Resume Tailor" project
2. Start a new chat
3. Attach your resume PDF and paste/type the job description
4. Claude will follow the multi-stage workflow from the instructions

### Limitations in Claude Desktop

| Feature | Claude Code | Claude Desktop |
|---------|------------|----------------|
| Automatic skill detection | Yes | No (manual project setup) |
| WebFetch for job URLs | Yes | No (paste JD text manually) |
| File system access | Yes (reads/writes files) | No (use attachments) |
| DOCX generation | Yes (via pandoc) | No (outputs markdown text) |
| Master profile persistence | Yes (saved to disk) | No (per-conversation only) |
| AskUserQuestion UI | Yes (structured choices) | No (free-text conversation) |

For full functionality, use Claude Code. Claude Desktop works well for the analysis, feedback, and draft generation stages — you'll just need to copy the output into a document manually.

### Alternative: MCP Server Setup (Advanced)

If you want Claude Desktop to access local files (for reading resumes and writing output), you can set up the [filesystem MCP server](https://github.com/anthropics/model-context-protocol-servers/tree/main/src/filesystem):

1. Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (Mac) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/yourname/Documents/resumes"
      ]
    }
  }
}
```

2. Restart Claude Desktop
3. Claude can now read/write files in the specified directory

## Locale Support

### English/US (EN)
- No photo, no DOB, no marital status
- Section order: Contact → Summary → Skills → Experience → Education
- 1-2 pages max
- Direct, metrics-forward tone

### Russian/CIS (RU)
- Photo and DOB expected
- Section order: Contact → Target Role → Education → Experience → Skills → Certifications → Languages
- 2-3 pages standard
- Professional formal tone
- Mixed RU + EN technical vocabulary

The skill asks which locale to use and applies the appropriate conventions automatically.

## How ATS Scoring Works

The skill estimates an ATS match score using:

```
Score = 0.4 × required_skills_coverage
      + 0.2 × nice_to_have_coverage
      + 0.2 × achievement_quantification_rate
      + 0.1 × section_completeness
      + 0.1 × keyword_distribution
```

- **85%+** — Strong match, likely passes ATS filters
- **70-84%** — Moderate, depends on competition
- **Below 70%** — Significant gaps to address

## Project Structure

```
resume-tailor-plugin/
├── .claude-plugin/
│   └── plugin.json                    # Plugin manifest
├── skills/
│   └── resume-tailor/
│       ├── SKILL.md                   # Main orchestrator (stages, workflow, checklist)
│       └── references/
│           ├── ats-rules.md           # ATS formatting rules & scoring
│           ├── locale-en.md           # English/US conventions
│           ├── locale-ru.md           # Russian/CIS conventions
│           └── section-templates.md   # Section templates for both locales
├── LICENSE
└── README.md
```

## License

MIT — see [LICENSE](LICENSE).

## Contributing

Issues and PRs welcome. If you find ATS rules that are outdated or locale conventions that need updating, please open an issue.
