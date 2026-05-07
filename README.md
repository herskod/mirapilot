# MIRA — Multidimensional Instrument for Recommending Academic track through AI

> **Reducing demographic bias in Italian secondary-school track recommendations using psychometric profiles and large language models.**

[![Demo](https://img.shields.io/badge/demo-live-brightgreen)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](#license)
[![Research](https://img.shields.io/badge/context-research%20prototype-orange)](#disclaimer)

---

## What is MIRA?

At the end of middle school in Italy (*terza media*), teachers issue a formal track recommendation directing each student toward an academic (*liceo*), technical, or vocational high school. A growing body of research documents that these recommendations are systematically biased by gender, immigrant origin, and socioeconomic background — independently of students' actual ability.

**MIRA** is an interactive web prototype built for a research project at Bocconi University (*Social Policy Challenge — Data, Inequality and Human Capital*). It demonstrates an AI-powered decision-support tool that:

1. Administers a three-instrument psychometric battery to students
2. Generates a structured orientation report using Claude (Anthropic API)
3. Flags mismatches between a teacher's track recommendation and the AI suggestion
4. Invites the teacher to confirm or revise their recommendation — with full transparency on the reasoning

The demographic information collected (gender, origin, parental education and occupation) is **never passed to the AI model**. The recommendation is based exclusively on test scores and vocational interests.

---

## Demo

The interface is a single self-contained HTML file. Open `MIRA.html` in any modern browser. An Anthropic API key is required only to generate the AI report in the teacher view.

### Student flow

| Step | Instrument | Purpose |
|------|-----------|---------|
| 1 | Background survey | Research data only — never used by the AI |
| 2 | CFT 20-R style | Fluid intelligence, curriculum-independent |
| 3 | INVALSI Italiano | Italian literacy, disaggregated by competency dimension |
| 4 | INVALSI Matematica | Mathematics, disaggregated by domain and cognitive level |
| 5 | Vocational interests | 60-item questionnaire across RIASEC categories (proxy for Magellano Junior 2) |

### Teacher view

- Select a student (three pre-loaded demo profiles + live session)
- ⚠️ **Segnalazione MIRA** badges flag students with a mismatch between teacher and AI recommendation — click to see the reasoning and choose **Confirm** or **Revise**
- Generate the full AI report via the Anthropic API
- Demo navigation bar (bottom of screen) allows jumping between sections without completing forms — useful for classroom presentations

---

## Repository structure

```
MIRA.html           # Complete single-file application (HTML + CSS + JS)
MIRA_presentation.tex   # LaTeX/Beamer slides for the 10-minute project presentation
README.md
```

All application logic, styles, test items, demo data, and the AI report generation are self-contained in `MIRA.html`. No build step or server required.

---

## Key design decisions

### Demographic firewall
Background variables (gender, country of birth, parental education and occupation) are collected for research purposes and stored in the client-side state, but are **explicitly excluded from the API payload**. The system prompt contains an anti-bias instruction requiring the recommendation to be based solely on test scores and vocational interests.

### Demo profiles
Three pre-loaded students illustrate different scenarios:

| Student | Profile | Teacher rec | AI rec | Flag |
|---------|---------|------------|--------|------|
| **Sofia Rossi** | CFT high (24/30), Math low (40%), Italian high (87%), Holland IAS | Liceo classico | Liceo scientifico | ⚠️ Yes — CFT/Math discrepancy |
| **Omar Bouazza** | CFT very high (26/30), Math high (80%), Italian 67%, Holland IRC | Istituto tecnico | Liceo scientifico | ⚠️ Yes — anti-bias demo |
| **Giulia Ferretti** | CFT average (18/30), Italian perfect (100%), Math 55%, Holland ASE | Liceo classico | Liceo classico | ✓ No discrepancy |

Omar's profile is the central anti-bias demonstration: a strong test profile consistently directed toward a lower track, illustrating how demographic background can distort recommendations independently of ability.

### Threshold validity — an honest note
The thresholds used to flag mismatches (e.g. CFT ≥ QI 105 for *licei*, INVALSI Math ≥ 60% for *Liceo Scientifico*) are **expert-judgment approximations** derived from INVALSI level descriptors and national score distributions — not empirically calibrated on Italian longitudinal data. The empirically correct quantity — P(successfully completing the *biennio* | enrolled in track T, INVALSI profile at grade 8) — requires access to the INVALSI-SNV longitudinal panel via the CINECA platform (`invalsi-areastatistica.cineca.it`). Estimating these conditional distributions is a priority for Phase 2 of the research project.

---

## AI report generation

The teacher view calls `https://api.anthropic.com/v1/messages` using `claude-sonnet-4-20250514`. The system prompt:

- Encodes a knowledge base for all six Italian secondary tracks
- Contains an explicit anti-bias instruction
- Instructs the model to mention the empirical limitation of the current thresholds in the *Nota Metodologica* section of every report
- Requests a structured Italian-language report with sections: Profilo di Interessi, Profilo Cognitivo e Accademico, Confronto con Studenti Simili, Raccomandazione AI, Nota Metodologica, and (if applicable) Segnalazione

No API key is bundled in the code. The browser must have access to the Anthropic API endpoint.

---

## Instruments — status and licensing

| Instrument | Status in prototype | Full implementation |
|-----------|-------------------|-------------------|
| CFT 20-R (Cattell) | SVG-based approximation | Licensed from Hogrefe Italy; psychologist-supervised administration |
| INVALSI assessments | Items inspired by 2015–16 national tests | Official INVALSI items (publicly available for research) |
| Magellano Junior 2 | Original 60-item RIASEC proxy | Licensed from Giunti Psychometrics; full 86-item professional interest subscale across 14 Italian sectors |

---

## Research context

**Project:** Social Policy Challenge — Data, Inequality and Human Capital, Bocconi University  
**Authors:** Yael Brianzi, Zoe Di Lieto, David Herskovits, Agnese Porro  
**Advise on assessment pipeline design:** Prof. Jacopo Di Tullio 

**Key references:**
- Carlana, M. (2019). Implicit stereotypes: Evidence from teachers' gender bias. *Quarterly Journal of Economics*, 134(3).
- Alesina, A., Carlana, M., La Ferrara, E., & Pinotti, P. (2024). Revealing stereotypes: Evidence from immigrants in schools. *American Economic Review*.
- Carlana, M. & La Ferrara, E. (2025). Apart but connected: Online tutoring, cognitive outcomes, and soft skills. *American Economic Review*.
- Miserocchi, S. (2023). Information and teacher bias in track recommendations.

---

## Disclaimer

> **This is a research prototype for a university course project. It is not intended for clinical use, real educational decisions, or deployment in schools. All test items are approximations of proprietary instruments. The AI-generated reports are illustrative and should not be used to make actual track recommendations about real students.**

---

## License

MIT — see `LICENSE` for details. Note that the CFT 20-R and Magellano Junior 2 instruments are proprietary; the approximations in this prototype are for academic demonstration purposes only.
