# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code and Codex plugin** (not a traditional software project) that provides a single skill: `resume-tailor`. It tailors resumes to specific job descriptions with ATS optimization. No build system, no tests, no dependencies — the project is entirely prompt-based markdown.

## Architecture

```
.claude-plugin/plugin.json    — Claude Code plugin manifest (name, version, metadata)
.codex-plugin/plugin.json     — Codex plugin manifest and marketplace metadata
.agents/plugins/marketplace.json — Codex self-hosted marketplace index
skills/resume-tailor/
  SKILL.md                    — Main skill definition (workflow, stages, rules)
  references/
    ats-rules.md              — ATS formatting rules, keyword strategy, scoring formula
    locale-en-ca.md           — English/CA Canada resume conventions
    locale-fr-ca.md           — French/CA Canada resume conventions
    locale-en.md              — English/US resume conventions
    locale-ru.md              — Russian/CIS resume conventions
    section-templates.md      — Section templates with slot markers for both English/US locale and Russian/CIS locale
    section-templates.md      — Section templates with slot markers for English/CA Canada locale and French/CA Canada locale
```

- `SKILL.md` is the orchestrator — it defines a 7-stage workflow (Stages 0-6) and references the files in `references/` at specific stages.
- Reference files are read by the skill at runtime, not by the plugin system. They contain domain knowledge, not code.
- The skill produces output in `docs/resume/` within the user's project (master profile, drafts, DOCX files). This directory is not part of the plugin itself.

## Key Concepts

- **Master profile** (`docs/resume/master-profile.md` in user's project): comprehensive career data store, created on first run, reused across tailoring sessions.
- **Visibility system**: roles in the master profile can be tagged `always`, `variant-specific`, `on-request`, or `reference-only` to control inclusion per resume variant.
- **ATS scoring**: weighted formula (40% required skills, 20% nice-to-have, 20% quantification, 10% completeness, 10% distribution).
- **Locale support**: EN/US and RU/CIS with different section ordering, personal info rules, and tone.

## External Dependency

DOCX generation requires `pandoc` installed on the user's system. The skill optionally uses `docs/resume/reference.docx` as a pandoc reference doc for styling.

## Git Conventions

- Do not add `Co-Authored-By` trailers to commit messages for AI agents.

## When Editing

- Changes to `SKILL.md` affect the entire workflow — the stages are sequential and interdependent.
- Reference files are self-contained domain knowledge; they can be updated independently.
- `.claude-plugin/plugin.json` follows the Claude Code plugin manifest schema.
- `.codex-plugin/plugin.json` follows the Codex plugin manifest schema. Keep shared metadata in sync.
- The plugin supports two locales (EN, RU). Changes to templates or conventions must be reflected in both locale files and `section-templates.md`.
