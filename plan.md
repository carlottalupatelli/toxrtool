# ToxRTool Web Application — Development Plan

## Executive Summary

A modern, single-page web application that digitises the EU JRC ToxRTool (Toxicological Data Reliability Assessment Tool), hosted on GitHub Pages. Users complete reliability assessments for *in vivo* or *in vitro* toxicity studies, receive automatic category scoring (1–4), and export results as a populated Excel file or print-ready PDF — all with zero back-end infrastructure.

**Live tool origin:** [JRC ToxRTool](https://joint-research-centre.ec.europa.eu/scientific-tools-and-databases/toxrtool-toxicological-data-reliability-assessment-tool_en)  
**Design reference:** Minimal, clean, token-economy aesthetic (≈ pricetoken.ai)  
**Hosting:** GitHub Pages (free, no server needed)

---

## 1. Technology Stack

| Layer | Choice | Reason |
|-------|--------|--------|
| Framework | **Vanilla HTML/CSS/JS** (no build step) | Zero config, direct GitHub Pages deployment |
| Styling | **CSS custom properties + minimal classes** | Full design control, no framework overhead |
| Typography | **Inter** (Google Fonts) | Clean, modern, scientific legibility |
| Icons | **Lucide Icons** (CDN) | Lightweight, consistent |
| Excel export | **SheetJS (xlsx.js)** via CDN | Faithful recreation of the original .xls format |
| Print/PDF | **Browser `window.print()`** + print stylesheet | No dependency, works universally |
| Persistence | **localStorage** | Save in-progress assessments |
| Hosting | **GitHub Pages** | Free static hosting, custom domain optional |
| Version control | **GitHub** | Public repo, enables contributions |

---

## 2. Repository Structure

```
toxrtool-web/
├── index.html              # Landing / study type selector
├── assess.html             # Assessment form (in vivo / in vitro)
├── results.html            # Results summary + export
├── css/
│   ├── base.css            # Reset, tokens, typography
│   ├── layout.css          # Grid, spacing, containers
│   ├── components.css      # Cards, buttons, form elements
│   ├── print.css           # Print stylesheet
│   └── theme.css           # Color tokens (light + dark)
├── js/
│   ├── data.js             # All criteria data (in vivo + in vitro)
│   ├── scoring.js          # Scoring logic, category calculation
│   ├── form.js             # Form rendering, validation, state
│   ├── export-excel.js     # SheetJS Excel generation
│   ├── export-print.js     # Print layout builder
│   └── storage.js          # localStorage save/load/clear
├── assets/
│   ├── logo.svg            # JRC / ECVAM badge (optional)
│   └── favicon.ico
├── templates/
│   └── toxrtool_base.xls   # Original file for reference
└── README.md
```

---

## 3. Full Data Model

### 3.1 General Study Information (both types)

Collected on step 1 of the form — mirrors the Excel "Study under evaluation" header block:

| Field ID | Label | Type | Required |
|----------|-------|------|----------|
| `study_type` | Study type | select: in_vivo / in_vitro | Yes |
| `authors` | Authors | text | No |
| `title` | Title | text | No |
| `source` | Testing facility / year / sponsor / study no. or bibliographic reference | textarea | No |
| `eval_date` | Date/period of evaluation | date | No |
| `eval_name` | Evaluator name | text | No |
| `eval_affiliation` | Evaluator affiliation | text | No |

### 3.2 In Vivo Criteria (21 criteria, 8 red/mandatory)

**Group I — Test Substance Identification (criteria 1–4)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 1 | Was the test substance identified? | ✅ |
| 2 | Is the purity of the substance given? | — |
| 3 | Is information on the source/origin of the substance given? | — |
| 4 | Is all information on the nature and/or physico-chemical properties of the test substance given that is necessary to characterise the study? | — |

**Group II — Test Organism Characterisation (criteria 5–9)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 5 | Is the species given? | ✅ |
| 6 | Is the sex of the test organism given? | — |
| 7 | Is information given on the strain of test animals plus, if considered necessary by the evaluator, on the supplier and colony conditions? | — |
| 8 | Is age or body weight of the test organisms at the start of the study given? | — |
| 9 | For repeated dose toxicity studies only (give point for other study types): Is information on the health status of test organisms given? | — |

**Group III — Study Design Description (criteria 10–16)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 10 | Is the administration route given? | ✅ |
| 11 | Are doses administered or concentrations in application media given? | ✅ |
| 12 | Are frequency and duration of exposure as well as time-points of observations explicitly stated? | ✅ |
| 13 | Were negative (where required) and positive controls (where required) included (give point also if not applicable)? | ✅ |
| 14 | Is the number of animals (in case of experimental human studies: number of test subjects) given for each dose group? | ✅ |
| 15 | Are sufficient details of the administration scheme given to judge the study (see explanations for details)? | — |
| 16 | For inhalation studies and repeated dose toxicity studies only (give point for other study types): Are housing and feeding conditions given? | — |

**Group IV — Study Results Documentation (criteria 17–19)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 17 | Are the study endpoint(s) and their method(s) of determination clearly described? | — |
| 18 | Is the description of the study results for all endpoints investigated transparent and reproducible? | — |
| 19 | Are the statistical methods applied for data analysis given and applied in a transparent and appropriate manner? | — |

**Group V — Plausibility of Study Design and Results (criteria 20–21)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 20 | Is the study design chosen appropriate for obtaining the substance-specific data needed? | ✅ |
| 21 | Are the quantitative study results reliable (see explanations for arguments)? | — |

**In Vivo Scoring Thresholds:**
- Category 1 (Reliable without restrictions): 18–21 points + ALL red criteria met
- Category 2 (Reliable with restrictions): 13–17 points + ALL red criteria met
- Category 3 (Not reliable): < 13 points, OR any red criterion not met
- Category 4 (Not assignable): Documentation insufficient

### 3.3 In Vitro Criteria (18 criteria, 6 red/mandatory)

**Group I — Test Substance Identification (criteria 1–4)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 1 | Was the test substance identified? | ✅ |
| 2 | Is the purity of the substance given? | — |
| 3 | Is information on the source/origin of the substance given? | — |
| 4 | Is all information on the nature and/or physico-chemical properties of the test substance given that is necessary to characterise the study? | — |

**Group II — Test System Characterisation (criteria 5–7)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 5 | Is the test system described? | — |
| 6 | Is information given on the source/origin of the test system? | — |
| 7 | Are necessary information on test system properties, and on conditions of cultivation/maintenance given? | — |

**Group III — Study Design Description (criteria 8–13)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 8 | Is the method of administration given (see explanations for details)? | — |
| 9 | Are doses administered or concentrations in application media given? | ✅ |
| 10 | Are frequency and duration of exposure as well as time-points of observations explicitly stated? | ✅ |
| 11 | Were negative controls included (give also point, if not necessary, see explanations)? | ✅ |
| 12 | Were positive controls included (give also point, if not necessary, see explanations)? | ✅ |
| 13 | Is the number of replicates (or complete repetitions of experiment) given? | — |

**Group IV — Study Results Documentation (criteria 14–16)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 14 | Are the study endpoint(s) and their method(s) of determination clearly described? | — |
| 15 | Is the description of the study results for all endpoints investigated transparent and reproducible? | — |
| 16 | Are the statistical methods for data analysis given and applied in a transparent and appropriate manner? | — |

**Group V — Plausibility of Study Design and Data (criteria 17–18)**

| No. | Criterion text | RED? |
|-----|---------------|------|
| 17 | Is the study design chosen appropriate for obtaining the substance-specific data needed? | ✅ |
| 18 | Are the quantitative study results reliable (see explanations for arguments)? | — |

**In Vitro Scoring Thresholds:**
- Category 1 (Reliable without restrictions): 15–18 points + ALL red criteria met
- Category 2 (Reliable with restrictions): 11–14 points + ALL red criteria met
- Category 3 (Not reliable): < 11 points, OR any red criterion not met
- Category 4 (Not assignable): Documentation insufficient

### 3.4 Results & Evaluator Input (both types)

| Field | Description |
|-------|-------------|
| **A** — Numerical Category | Auto-calculated from total score |
| **B** — Revised Category | Auto-adjusted if any red criterion not met |
| **C** — Evaluator's Proposal | Manual input (dropdown: 1, 2, 3, 4) |
| **D** — Deviation Justification | Free text (shown only if C ≠ B) |

### 3.5 Optional Relevance Section (both types)

| Field | Type |
|-------|------|
| Purpose of evaluation | textarea |
| Study conducted according to recent OECD or EU guidelines? | yes/no/n-a |
| (If not guideline study) Does a guideline exist? | yes/no/n-a |
| Relevant deviations from guideline(s) observed? | yes/no + textarea |
| Observations with importance to regulatory use of data? | yes/no + textarea |
| General comments on usability of the data | textarea |

---

## 4. Scoring Logic (`scoring.js`)

```javascript
function calculateScores(studyType, scores) {
  // scores: { 1: 0|1, 2: 0|1, ... }
  const redCriteria = RED_CRITERIA[studyType]; // e.g. [1,5,10,11,12,13,14,20]
  const thresholds = THRESHOLDS[studyType];    // { cat1: [18,21], cat2: [13,17] }

  const total = Object.values(scores).reduce((a, b) => a + b, 0);
  const allRedMet = redCriteria.every(n => scores[n] === 1);

  // Result A: based purely on score
  let resultA;
  if (total >= thresholds.cat1[0]) resultA = 1;
  else if (total >= thresholds.cat2[0]) resultA = 2;
  else resultA = 3;

  // Result B: downgrade to 3 if any red criterion failed
  const resultB = (resultA <= 2 && !allRedMet) ? 3 : resultA;

  return { total, allRedMet, resultA, resultB, redCriteria };
}
```

**Category labels:**
- 1 → "Reliable without restrictions"
- 2 → "Reliable with restrictions"
- 3 → "Not reliable"
- 4 → "Not assignable (documentation insufficient)"

---

## 5. Page & UX Flow

### Page 1 — Landing (`index.html`)

**Sections:**
1. **Hero** — Tool name, JRC attribution, one-line description
2. **What is ToxRTool?** — Brief explanation (purpose, origin, EU/OECD context)
3. **How it works** — 3-step visual: Select type → Score criteria → Export
4. **Start Assessment** — Two prominent cards:
   - 🔬 **In Vivo** — 21 criteria · 8 mandatory
   - 🧫 **In Vitro** — 18 criteria · 6 mandatory
5. **Footer** — JRC attribution, disclaimer, GitHub link

### Page 2 — Assessment (`assess.html`)

**Layout:** Sticky progress header + scrollable form

**Header (sticky):**
- Study type badge (In Vivo / In Vitro)
- Progress bar: `X / 21 criteria answered`
- Live score counter + current category badge
- Save / Continue Later button

**Step 1 — Study Information**
- All general info fields (authors, title, source, date, evaluator)
- "Begin Assessment →" button

**Step 2 — Criteria Scoring (Groups I–V)**

For each group:
```
┌─ Group I: Test Substance Identification ──────────────┐
│  Score: 3/4 ● ●●○                                     │
│                                                        │
│  🔴 1. Was the test substance identified?   [0] [1]   │
│       ℹ️  [tooltip on hover]                           │
│       Evaluator comments: ___________________          │
│                                                        │
│  2. Is the purity of the substance given?  [0] [1]    │
│  ...                                                   │
└────────────────────────────────────────────────────────┘
```

**Criterion UI features:**
- Red dot + red label for mandatory criteria
- Toggle buttons: `0` (grey) / `1` (green) — NOT a dropdown
- Optional evaluator comments textarea per criterion (collapsible)
- Tooltip icon (ℹ️) with explanation text
- Unanswered criteria highlighted in amber at submission

**Step 3 — Results & Evaluator Input**
- Score summary table (by group)
- **Result A** badge (auto)
- **Result B** badge (auto, with warning if downgraded)
- **Result C** — evaluator override dropdown
- **Result D** — justification textarea (shown if C ≠ B)
- Optional relevance section (collapsed by default, expandable)

### Page 3 — Results Summary (`results.html` or inline)

- Full assessment summary card
- Visual category badge (colour-coded: green=1, yellow=2, red=3, grey=4)
- Criteria breakdown by group
- Red criteria pass/fail checklist
- Export buttons:
  - **📥 Download Excel** (faithfully structured like original .xls)
  - **🖨️ Print / Save as PDF**
  - **🔗 Copy shareable link** (state encoded in URL hash)

---

## 6. Design System

### Colour Tokens (light mode, CSS variables)

```css
--color-bg:          #0a0a0f;   /* near-black background */
--color-surface:     #13131a;   /* card surface */
--color-border:      #1e1e2e;   /* subtle borders */
--color-text:        #e8e8f0;   /* primary text */
--color-muted:       #6b6b8a;   /* secondary text */
--color-accent:      #7c6af5;   /* purple accent (buttons, links) */
--color-accent-glow: rgba(124,106,245,0.15); /* glow effect */
--color-red:         #e05252;   /* mandatory/error */
--color-green:       #52d08a;   /* pass/cat1 */
--color-yellow:      #e0b352;   /* warning/cat2 */
--color-grey:        #4a4a6a;   /* neutral/cat4 */
```

### Typography Scale

```css
--font-sans:   'Inter', system-ui, sans-serif;
--font-mono:   'JetBrains Mono', monospace;  /* scores, numbers */

--text-xs:   0.75rem;   /* 12px — labels, badges */
--text-sm:   0.875rem;  /* 14px — body small */
--text-base: 1rem;      /* 16px — body */
--text-lg:   1.125rem;  /* 18px — lead */
--text-xl:   1.25rem;   /* 20px — subheadings */
--text-2xl:  1.5rem;    /* 24px — headings */
--text-4xl:  2.25rem;   /* 36px — hero */
```

### Component Catalogue

| Component | Description |
|-----------|-------------|
| `CriterionCard` | Container for one criterion: number, text, red badge, score toggle, comment |
| `GroupSection` | Collapsible group with header, group score, progress ring |
| `ScoreBadge` | Coloured pill: "Category 1 — Reliable without restrictions" |
| `ProgressBar` | Horizontal fill bar, animated |
| `ToggleScore` | Pill-shaped `0` / `1` toggle (keyboard accessible) |
| `Tooltip` | Hover card with criterion explanation |
| `ResultCard` | A/B/C/D result block with colour-coded outcome |
| `ExportButton` | CTA button with icon, loading state |

---

## 7. Excel Export Specification

The exported `.xlsx` faithfully replicates the original tool's sheet structure:

**Sheet 1: Rater** — Evaluator general info fields  
**Sheet 2: Explanations** — Static reference text (read-only, copied from original)  
**Sheet 3: in_vivo or in_vitro** — Populated assessment with:
  - Study header info
  - All criteria rows with scores filled
  - Evaluator comments in column D
  - Group subtotals auto-calculated
  - Results A, B, C, D filled
  - Red cell formatting applied to mandatory criteria labels
  - Orange/light-blue result cells preserved
  - Optional relevance section filled if completed

**Implementation (SheetJS):**
```javascript
import * as XLSX from 'https://cdn.sheetjs.com/xlsx-latest/package/xlsx.mjs';

function buildWorkbook(assessment) {
  const wb = XLSX.utils.book_new();
  wb.Props = { Title: "ToxRTool Assessment", Author: assessment.evalName };
  
  // Sheet: assessment
  const wsData = buildAssessmentSheet(assessment);
  const ws = XLSX.utils.aoa_to_sheet(wsData);
  applyStyles(ws, assessment); // cell colors, widths
  XLSX.utils.book_append_sheet(wb, ws, assessment.studyType === 'in_vivo' ? 'in_vivo' : 'in_vitro');
  
  XLSX.writeFile(wb, `ToxRTool_${assessment.studyType}_${date}.xlsx`);
}
```

---

## 8. Print Stylesheet

When `window.print()` is called, `print.css` activates:
- White background, black text
- Criteria rendered as a clean table (no toggle buttons)
- Score shown as `0` or `1` in each row
- Red criteria marked with `*` and footnote
- Group subtotals and final results clearly boxed
- JRC attribution in footer
- Page breaks between sections
- Page numbers (`@page` rule)

---

## 9. URL State Encoding (Shareable Links)

Completed or partial assessments encoded in the URL hash as compressed JSON:
```
assess.html#v1_eyJzdHVkeVR5cGUiOiJpbl92aXZvIiwic2NvcmVzIjp7IjEiOjEsIjIiOjAsI...
```
- Uses `LZString` (CDN) for compression
- Max ~2KB of state fits comfortably in URLs
- "Copy Link" button generates and copies to clipboard

---

## 10. GitHub Pages Deployment

### Step-by-step setup:

1. Create public repo: `github.com/[username]/toxrtool-web`
2. Push all static files to `main` branch
3. Settings → Pages → Source: `main` / `/ (root)`
4. Live at: `https://[username].github.io/toxrtool-web/`

### Optional custom domain:
- Add `CNAME` file with `toxrtool.yourdomain.com`
- Configure DNS CNAME → `[username].github.io`

### CI/CD (optional GitHub Action):
```yaml
# .github/workflows/deploy.yml
on: push
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/deploy-pages@v4  # auto-deploys on push to main
```

---

## 11. Accessibility & Compliance

- WCAG 2.1 AA compliant
- All form inputs have `<label>` associations
- Toggle buttons use `role="group"` + `aria-pressed`
- Keyboard navigation: Tab through all criteria, Space to toggle
- Screen reader announcements for live score updates (`aria-live="polite"`)
- Sufficient colour contrast (≥ 4.5:1 for all text)
- Disclaimer: "This web tool is not affiliated with the European Commission. The ToxRTool methodology is developed by EURL ECVAM / JRC."

---

## 12. Implementation Phases

### Phase 1 — Core (Week 1–2)
- [ ] Repo setup + GitHub Pages config
- [ ] `data.js` — all criteria, explanations, metadata
- [ ] `scoring.js` — complete scoring engine + unit tests
- [ ] `index.html` — landing page + design system CSS
- [ ] `assess.html` — full form rendering (both study types)
- [ ] All criteria toggle inputs working
- [ ] Live score calculation

### Phase 2 — Results & Export (Week 3)
- [ ] Results section (A/B/C/D) auto-populates
- [ ] Optional relevance section
- [ ] `export-excel.js` — SheetJS integration
- [ ] `print.css` — print-ready layout
- [ ] `storage.js` — auto-save to localStorage
- [ ] Load previously saved assessment

### Phase 3 — Polish & UX (Week 4)
- [ ] URL hash state encoding (shareable links)
- [ ] Tooltips with full criterion explanations
- [ ] Unanswered criteria validation + scroll-to-error
- [ ] Mobile responsive layout
- [ ] Smooth animations (progress bar, score counter)
- [ ] Dark mode (default) + optional light mode toggle

### Phase 4 — Launch
- [ ] README with usage instructions + attribution
- [ ] Disclaimer and legal notice page
- [ ] Test cross-browser (Chrome, Firefox, Safari, Edge)
- [ ] Lighthouse audit (target: 95+ performance, 100 accessibility)
- [ ] Submit to JRC/ECVAM as community resource (optional)

---

## 13. Key Design Decisions & Rationale

| Decision | Rationale |
|----------|-----------|
| No framework (React/Vue) | Zero build step = direct GitHub Pages deploy; simpler contribution model |
| Toggle buttons (not dropdown) | Faster to use; dropdown was a UX limitation of the Excel original |
| Dark-first design | Matches pricetoken.ai reference; reduces eye strain for long assessments |
| SheetJS for export | Produces true `.xlsx`; binary-compatible with original tool |
| URL state sharing | Enables collaboration without a database |
| localStorage auto-save | Prevents data loss; common in long-form scientific workflows |
| Mandatory criteria visually distinct | Red dot + red text mirrors the original Excel convention |
| Group progress rings | Instant visual feedback; helps evaluators track progress within each group |

---

## 14. Attribution & Legal

- Tool methodology: © European Commission, JRC/EURL ECVAM  
- Reference: Schneider et al. (2009) *Regulatory Toxicology and Pharmacology*, 54(2):162–173
- Website implementation: Open source (MIT License)
- No EU Commission endorsement implied
- Data never leaves the user's browser (no server, no analytics unless opted-in)

---

*This plan was compiled from the official ToxRTool Excel file (toxrtool.xls) and user manual (PDF), EURL ECVAM, European Commission Joint Research Centre.*