# Word Layout Configuration (template reference)

Structural analysis of the `.docx` file (raw XML), meant to be reused as parameters in a resume-generation `SKILL.md`.

## 1. Page

| Parameter | Value (DXA) | Equivalent |
|---|---|---|
| Page size | `12240 x 15840` | US Letter (8.5" x 11") |
| Top margin (page 1, header block) | `589` | ~0.41" |
| Top margin (following sections) | `645` | ~0.45" |
| Left/right margin | `1440` | 1" |
| Bottom margin | `1066` to `1440` (varies by section) | 0.74"–1" |
| Header | `0` | no reserved space (header is visually empty) |
| Footer | `336` | ~0.23" |
| Page numbering | starts at 1 (`pgNumType start=1`) |

## 2. Section structure (`w:sectPr`)

The document uses **3 continuous sections** with different column configurations:

1. **Section 1 — header block** (name, city, phone, email): 1 column, centered.
2. **Section 2 — resume body**: `w:cols num="2"` — **2 uneven columns**
   - Left column: `4326` DXA wide
   - Gap between columns: `708` DXA
   - Right column: `4326` DXA wide
3. **Section 3 — end of document** (last experience entry): back to 1 column.

> Reusable trick: the CV uses **no tables** for layout. The two-column effect is achieved purely through continuous section breaks (`w:type val="continuous"`) + `w:cols`.

## 3. Fonts and colors

| Element | Font | Size | Color | Style |
|---|---|---|---|---|
| Document default font | Proxima Nova (defined but never actually used) | 11pt | black | — |
| Name (header) | Arial | 16pt (`sz=32`) | black | centered |
| Contact info (city, phone, email) | Arial | 11pt (default size) | black (hyperlink uses `Hyperlien` style) | centered |
| Section headings (RESUMÉ DU PROFIL, COMPÉTENCES TECHNIQUES, etc.) | Arial | 12pt (`sz=24`) | **`#205968`** (dark teal) | **bold** + single black bottom border (`sz=8`, ~1pt) |
| Job title / employer line | Arial | 11pt | black | **bold** |
| Body text / bullets | Arial | 11pt | black | normal |

> The single accent color used throughout the document is `#205968`, reserved exclusively for section headings. No other color, no shading, no colored fills anywhere.

## 4. Section headings — exact recipe

Each section heading (`RESUMÉ DU PROFIL`, `COMPÉTENCES TECHNIQUES`, `EXPERIENCE DE TRAVAIL`, etc.) does **not** use the built-in `Heading 1/2` styles — it's all **direct formatting**:

```
Font: Arial, bold, 12pt, color #205968
Spacing: line=276 (line spacing ~1.15)
Border: bottom, single, 8 (1pt), color #000000, space=1
```

## 5. "Job title + dates" line (work experience)

Uses **tab stops** (not a table) to align the job title on the left and the dates on the right:

```
w:tabs: positions 4395 / 8364 / 8647 (DXA)
Job title: bold
Dates: bold
Space before paragraph: 240 DXA (~0.17")
```

## 6. Bullet lists

| Parameter | Value |
|---|---|
| Level 0 | bullet `●`, font *Noto Sans Symbols*, left indent `720`, hanging indent `360` |
| Level 1 | bullet `o`, font *Courier New*, left indent `1440` |
| Level 2 | bullet `▪`, left indent `2160` |
| Actual indent observed (body) | `left=714`, `hanging=357` (slightly tighter than the numbering definition's defaults) |
| Bullet line spacing | `line=259` (~1.08), `before=0` |

## 7. Document-wide default spacing (`docDefaults`)

```xml
<w:spacing w:before="80" w:line="288" w:lineRule="auto"/>
<w:widowControl w:val="0"/>
```
→ 4pt before each paragraph, 1.2x line spacing, no widow/orphan control.

## 8. Notably absent elements (worth noting to avoid over-designing)

- No tables (`w:tbl`) anywhere — everything relies on section columns + tab stops.
- No shading (`w:shd`) — no colored cell or paragraph background.
- No graphic sidebar, no image, no icons.
- A single accent color (`#205968`), used only for section headings and their border.
- The Word header (`header1.xml`) is present but has **no visible content** — only leftover tab stops.

## 9. Actionable summary for a template

```yaml
page: { size: Letter, margins: { top: 0.41-0.45in, bottom: 0.74-1in, left/right: 1in } }
layout: 2 uneven columns (4326 / 708 gap / 4326 DXA) via continuous section, no table
font_family: Arial (everywhere, despite a default Proxima Nova that's never applied)
accent_color: "#205968"
section_heading: { font: Arial 12pt bold, color: accent, border_bottom: 1pt solid black, line_spacing: 1.15 }
job_line: { font: Arial 11pt bold, tabs: [4395, 8364, 8647], space_before: 240 twips }
bullets: { level0_char: "●", indent_left: 714, hanging: 357, line_spacing: ~1.08 }
paragraph_default_spacing: { before: 80 twips (4pt), line: 288 (1.2x) }
```
