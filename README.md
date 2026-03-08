# ToxRTool Web

A modern, open-source web implementation of the **ToxRTool** — the Toxicological Data Reliability Assessment Tool developed by EURL ECVAM at the European Commission Joint Research Centre.

## Features

- ✅ Full **in vivo** assessment (21 criteria, 8 mandatory)
- ✅ Full **in vitro** assessment (18 criteria, 6 mandatory)
- ✅ Automatic scoring → Categories A and B
- ✅ Evaluator override (Category C) with justification (D)
- ✅ Optional regulatory relevance section
- ✅ **Export to Excel (.xlsx)** — faithful multi-sheet format
- ✅ **Print / Save as PDF** via browser
- ✅ Auto-save to browser localStorage
- ✅ **Shareable links** — encode full assessment state in URL hash
- ✅ Zero backend — runs entirely in the browser

## Live Demo

👉 https://carlottalupatelli.github.io/toxrtool/

## Usage

1. Open `index.html` and select study type (In Vivo / In Vitro)
2. Fill in study and evaluator information
3. Score all criteria (0 = not met, 1 = met)
4. Review auto-calculated categories A and B
5. Enter your proposal (C) and justification if deviating
6. Download Excel or print

## Deployment to GitHub Pages

1. Fork / clone this repo
2. Push to `main`
3. Go to **Settings → Pages → Source: main / root**
4. Site is live at `https://carlottalupatelli.github.io/toxrtool/`

## Attribution

**Methodology:** Schneider K. et al. (2009). *ToxRTool, a new tool to assess the reliability of toxicological data.* Regulatory Toxicology and Pharmacology, 54(2), 162–173.

**Original tool:** [EURL ECVAM / JRC](https://joint-research-centre.ec.europa.eu/scientific-tools-and-databases/toxrtool-toxicological-data-reliability-assessment-tool_en)

This web implementation is an unofficial open-source tool. Not affiliated with or endorsed by the European Commission.

## License

MIT
