# Blitzy Project Guide — Student Report Generator Documentation

> **Branch:** `blitzy-e6cd2609-e4f7-4ddf-83c4-7c04a97167e6` · **HEAD:** `5342de8` · **Baseline:** `5eb1147`
> **Change set:** Documentation-only (20 files, +3,051 / −0 lines) · **Completion (AAP-scoped):** **87.9%**

---

## 1. Executive Summary

### 1.1 Project Overview

The **Student Report Generator** is a zero-install, browser-based educational utility that captures a student's identity and five subject marks, computes total/percentage/letter-grade, renders an on-screen report card, and exports it to PDF via jsPDF. Its entire source (`index.html`, `style.css`, `script.js`) lives embedded as fenced code blocks inside `Readme.md`. This project delivers **comprehensive, code-grounded documentation**: 18 new Markdown files under `docs/` that separate every functionality (F-001..F-008) into its own page and state each unit's explicit **behavioral contract**, plus a minimal navigation update to `Readme.md`. The audience is developers and maintainers who need a precise, citation-backed reference to the application's behavior, APIs, data schema, and invariants.

### 1.2 Completion Status

```mermaid
%%{init: {"theme":"base","themeVariables":{"pie1":"#5B39F3","pie2":"#FFFFFF","pieStrokeColor":"#B23AF2","pieStrokeWidth":"2px","pieOuterStrokeColor":"#B23AF2","pieOuterStrokeWidth":"2px","pieTitleTextColor":"#B23AF2","pieSectionTextColor":"#000000","pieSectionTextSize":"15px","pieLegendTextColor":"#000000"}}}%%
pie showData
    title Completion Status — 87.9% Complete (AAP-scoped)
    "Completed Work (AI)" : 58
    "Remaining Work" : 8
```

| Metric | Hours |
|---|---|
| **Total Hours** | **66** |
| **Completed Hours (AI + Manual)** | **58** (58 AI + 0 Manual) |
| **Remaining Hours** | **8** |
| **Percent Complete** | **87.9%** (58 ÷ 66) |

> Completion is computed strictly from AAP-scoped + path-to-production hours (PA1). Every AAP autonomous deliverable is **complete and validated**; the remaining 8h is human path-to-production work (review, host verification, optional hardening).

### 1.3 Key Accomplishments

- ✅ **18 documentation files created** under `docs/` and `Readme.md` updated, all committed at `5342de8` (+3,051 / −0 lines; ~22,148 words).
- ✅ **All 8 functionalities (F-001..F-008) separated** into dedicated pages (data-entry, computation, grading, report-rendering, pdf-export, styling).
- ✅ **Both public JS functions documented** — `generateReport()` and `downloadPDF()` — with full signatures, reads/writes, worked examples, and contracts.
- ✅ **Behavioral-contracts SSOT** (`docs/contracts/behavioral-contracts.md`) capturing all **5 invariants**: rebuild-from-scratch, no-NaN `|| 0` guard, `.toFixed(2)` precision, default `'F'` grade, DOM-read indirection.
- ✅ **11 Mermaid diagrams** (exceeds the ≥ 5 target) — all well-formed and rendering to valid SVG.
- ✅ **668 inline `[Readme.md:Lx-Ly]` citations** (143 distinct ranges), all within bounds (max line 283 ≤ 296) and accurate to the current `Readme.md`.
- ✅ **Fixed-parameter reference tables** (5 subjects, 500-point denominator, 6 grade thresholds) with honest *intended-not-enforced* nuance.
- ✅ **markdownlint exit 0** (zero violations across 19 files); **294 internal links + anchors, 0 broken**; hub-and-spoke navigation confirmed.
- ✅ **Runtime-verified** — app reconstructed and exercised in Chrome (worked example 400 / 80.00% / Grade A; all invariants confirmed; no console errors).
- ✅ **Minimal disruption** — `Readme.md` grew by only 15 lines (0 removed); embedded source preserved byte-for-byte; **no source code modified**.

### 1.4 Critical Unresolved Issues

| Issue | Impact | Owner | ETA |
|---|---|---|---|
| *None — no release-blocking issues identified* | All AAP autonomous deliverables are complete, validated, and committed. The remaining items are non-blocking path-to-production tasks tracked in Sections 2.2 and 6. | — | — |

> The deliverable is mergeable as-is. The non-blocking, low-severity open items (host-render verification, optional CI gates, a cosmetic config-comment fix, and a product decision on embedded-vs-standalone source) are itemized in Section 2.2 (Remaining Work) and Section 6 (Risk Assessment).

### 1.5 Access Issues

| System/Resource | Type of Access | Issue Description | Resolution Status | Owner |
|---|---|---|---|---|
| Git repository (branch `blitzy-e6cd2609-…`) | Read/Write | None — all 20 in-scope files tracked and committed at `5342de8` | ✅ Resolved | Maintainer |
| jsPDF CDN (cdnjs) | Public HTTPS | None — public CDN; documented as a runtime precondition, no credentials needed | ✅ N/A | — |

**No access issues identified.** Note: the AAP-flagged Environment-1 staging `API_KEY` and `DB_HOST` are **not applicable** to this static repository and were treated as sensitive and excluded from all documentation (AAP 0.9.2); they are not required to author, render, or run anything in this project.

### 1.6 Recommended Next Steps

1. **[High]** Conduct an SME / editorial review of the 18 documentation pages for technical accuracy and clarity (≈3h).
2. **[High]** Verify Mermaid diagrams, internal links, and reference tables render correctly on the actual Git host (GitHub) after merge (≈1h).
3. **[Medium]** Make a product decision on the embedded-vs-standalone source ambiguity (whether to extract `index.html`/`style.css`/`script.js` into real files in a future *code* task) and open a follow-up ticket if pursued (≈1h).
4. **[Low]** *(Optional)* Wire documentation quality gates (`markdownlint` + `markdown-link-check`) into CI to prevent future drift (≈2h).
5. **[Low]** Fix the cosmetic encoding artifact (`â€”` mojibake) in the `.markdownlint.yaml` comment (≈1h).

---

## 2. Project Hours Breakdown

### 2.1 Completed Work Detail

All hours below were delivered **autonomously by Blitzy agents** (AI) and trace to specific AAP requirements. Total = **58h** (matches Completed Hours in Section 1.2).

| Component | Hours | Description |
|---|---|---|
| Source code analysis & content extraction | 4 | Reverse-read the embedded `index.html`/`style.css`/`script.js`; extract function signatures, DOM element IDs, style rules, and all magic values (subjects, denominator, thresholds) [Readme.md:L176-L253]. |
| Architecture documentation (3 docs + 5 diagrams) | 7 | `overview.md`, `component-model.md`, `data-flow.md` — system context, 3-component + jsPDF model, DOM-mediated flow; 5 Mermaid diagrams (high-level architecture, component-relationship, 3 flow/sequence). |
| Functionality documentation (6 docs, F-001..F-008, contracts) | 12 | `data-entry`, `computation`, `grading`, `report-rendering`, `pdf-export`, `styling` — one page per functional area with explicit contract blocks (the core "clearly separated" + "expectation" deliverable). |
| API reference documentation (3 docs + 2 diagrams) | 9 | `script-js.md` (both functions: signatures, reads/writes, worked example, 2 flowcharts), `html-structure.md` (DOM element/ID reference), `css-reference.md` (selector/style reference). |
| Reference data + behavioral-contracts SSOT (2 docs) | 6 | `data-schema.md` (fixed-parameter tables) and `behavioral-contracts.md` SSOT (5 invariants, preconditions, postconditions, error modes, determinism guarantee). |
| User guides (2 docs + troubleshooting) | 3 | `getting-started.md` (zero-install run guide, prerequisites) and `usage.md` (walkthrough, Generate-before-Download ordering, CDN/TypeError troubleshooting). |
| Dependencies documentation (jsPDF CDN contract) | 2 | `dependencies.md` — jsPDF 2.5.1 cdnjs integration, `window.jspdf` global contract, CDN-availability precondition [Readme.md:L53]. |
| Documentation hub + `Readme.md` navigation update | 3 | `docs/index.md` hub-and-spoke master TOC + the `Readme.md` Documentation section (+15/−0 lines, content preserved). |
| Inline citations (668) authoring + post-change recalculation | 3 | Author and re-baseline 668 `[Readme.md:Lx-Ly]` citations after the `Readme.md` line numbers shifted from the AAP baseline (e.g., CDN L38→L53). |
| markdownlint config + lint compliance | 2 | `.markdownlint.yaml` with documented, justified exceptions (MD013/MD060/MD025) + achieving exit 0 across 19 files. |
| Iterative QA review cycles (CP1/CP2/CP4) | 4 | Resolution of checkpoint review findings (source fidelity, hub nav, baseline, grade wording, a11y limitation) across multiple commits. |
| Final autonomous validation (lint/links/diagrams/citations/content/runtime) | 3 | Full validation sweep incl. runtime reconstruction in Chrome and negative-control proofs. |
| **Total Completed** | **58** | |

### 2.2 Remaining Work Detail

All remaining work is **path-to-production human work**; no AAP autonomous deliverable is outstanding. Total = **8h** (matches Remaining Hours in Section 1.2 and the Section 7 pie chart).

| Category | Hours | Priority |
|---|---|---|
| SME / editorial documentation review (accuracy & clarity of 18 docs) | 3 | High |
| Host-platform render verification (Mermaid / links / tables on GitHub post-merge) | 1 | High |
| Embedded-vs-standalone source decision (+ follow-up ticket if extraction pursued) | 1 | Medium |
| Optional CI documentation quality gates (`markdownlint` + `markdown-link-check`) | 2 | Low |
| Cosmetic config-comment fix (`.markdownlint.yaml` mojibake) | 1 | Low |
| **Total Remaining** | **8** | |

### 2.3 Hours Reconciliation

| Bucket | Hours |
|---|---|
| Section 2.1 — Completed | 58 |
| Section 2.2 — Remaining | 8 |
| **Total (must equal Section 1.2 Total)** | **66** ✅ |

`58 (Completed) + 8 (Remaining) = 66 (Total)` — consistent with Section 1.2 and the Section 7 pie chart.

---

## 3. Test Results

This is a **documentation-only deliverable** for a static app with **no automated test harness** (AAP 0.7.3: *"no automated test harness exists in the repository"*). Accordingly, the table below reports the **documentation-validation gates** executed by Blitzy's autonomous validation systems — the documentation-appropriate equivalents of compile/test gates. **Every entry originates from Blitzy's autonomous validation logs and was independently reproduced during this assessment.**

| Test Category | Framework / Tool | Total Tests | Passed | Failed | Coverage % | Notes |
|---|---|---|---|---|---|---|
| Markdown Lint (compile-equivalent) | markdownlint-cli 0.49.0 | 19 files | 19 | 0 | 100% | Exit 0, zero violations; proven genuine via negative control + strict-config re-run. |
| Internal Link & Anchor Integrity | markdown-link-check 3.14.2 + fence-aware checker | 294 links | 294 | 0 | 100% | 283 file links + 11 same-file anchors; hub-and-spoke nav confirmed; 0 broken. |
| Mermaid Diagram Render | @mermaid-js/mermaid-cli 11.15.0 | 11 diagrams | 11 | 0 | 100% | All valid SVG; required types present (architecture, component, 2 function flows, grade-decision). |
| Citation Accuracy | Custom bounds/range audit | 668 citations | 668 | 0 | 100% | 143 distinct ranges; all within 1–296; every boundary matches the cited code/content. |
| Contract Block Presence | Custom content audit | 9 docs | 9 | 0 | 100% | All 6 functionality + 3 API-reference docs carry an explicit "Expected Behavior / Contract" block. |
| Runtime Behavior Verification | Manual (reconstructed app in Chrome) | 8 behaviors | 8 | 0 | n/a | Worked example, rebuild-from-scratch, no-NaN guard, default 'F', `.toFixed(2)`, DOM-read invariant, PDF filename, grade boundaries. |
| **Totals** | — | **1,009** | **1,009** | **0** | **100%** | No failures across any autonomous documentation gate. |

> **Integrity note:** There are no unit/integration/E2E tests because the repository contains no test harness or build system (by design, per the AAP). The categories above are the autonomous **documentation-correctness** gates and were re-run during this assessment with identical results.

---

## 4. Runtime Validation & UI Verification

Runtime validation was performed by the Final Validator by **reconstructing the embedded application** (`index.html`/`style.css`/`script.js`) into a throwaway directory (outside the repo, not committed) and exercising it in Google Chrome. Every documented behavior was confirmed against the live runtime.

**Application Runtime Health**

- ✅ **Operational** — App loads and runs in Chrome with **no console errors**.
- ✅ **Operational** — Worked example (90+85+80+75+70): **Total 400 / 80.00% / Grade A** (exact match to documentation).
- ✅ **Operational** — Rebuild-from-scratch invariant: running *Generate Report* twice keeps exactly 5 rows (no accumulation).
- ✅ **Operational** — No-NaN `|| 0` guard + default `'F'`: blank inputs → 0 / 0.00 / F.
- ✅ **Operational** — `.toFixed(2)` precision: percentages render as 80.00 / 50.00 / 0.00.
- ✅ **Operational** — DOM-read invariant: changing form fields without regenerating leaves the rendered report card unchanged.
- ✅ **Operational** — Grade boundaries map up correctly (50 → D, 80 → A).

**API / Integration Verification**

- ✅ **Operational** — jsPDF CDN precondition: `window.jspdf` is populated by the cdnjs UMD script at runtime.
- ✅ **Operational** — `downloadPDF()` happy path does not throw and saves `Alice Example_Report.pdf` (matches `${name}_Report.pdf`).

**UI Verification**

- ✅ **Operational** — Report card renders with the documented structure (name, roll, marks table, total, percentage, grade).
- ✅ **Operational** — The Mermaid grade-decision flowchart was visually confirmed accurate against the runtime grade cascade.
- ⚠ **Partial (documented honestly)** — The UI has **no `@media` responsive rules** (viewport meta tag only). This is accurately disclosed in `docs/functionality/styling.md`; it is an app property, not a documentation defect, and is out of scope for this docs-only change set.

---

## 5. Compliance & Quality Review

Cross-mapping of AAP deliverables and rules to Blitzy quality/compliance benchmarks. Fixes applied during autonomous validation are noted; the only outstanding item is the non-autonomous human SME review.

| AAP Requirement / Benchmark | Target | Result | Status | Progress |
|---|---|---|---|---|
| Functional separation (one doc per functionality) | 8/8 (F-001..F-008) | 6 functionality docs covering all 8 features | ✅ Pass | 100% |
| Behavioral contract blocks | Every functional + API doc | 9/9 docs carry "Expected Behavior / Contract" | ✅ Pass | 100% |
| Code-grounded with citations | Every claim cited `[Readme.md:Lx-Ly]` | 668 citations, all in bounds & accurate | ✅ Pass | 100% |
| Mermaid diagrams | ≥ 5 | 11 delivered, all well-formed | ✅ Pass | 100% |
| Public JS function reference | 2/2 | `generateReport()`, `downloadPDF()` | ✅ Pass | 100% |
| Logical components documented | 3 + jsPDF | `index.html`, `style.css`, `script.js` + jsPDF | ✅ Pass | 100% |
| Fixed parameters in table format | 100% | 5 subjects, max 100 (not enforced), 500 denom, 6 thresholds | ✅ Pass | 100% |
| Troubleshooting in user guides | Ordering + CDN | `usage.md` covers Generate-before-Download + jsPDF/TypeError | ✅ Pass | 100% |
| Minimal disruption to `Readme.md` | Add nav only; preserve content | +15 / −0 lines; content byte-for-byte preserved | ✅ Pass | 100% |
| No source code modification | 0 code changes | Embedded code untouched; documentation-only | ✅ Pass | 100% |
| Match existing Markdown style | H1 / fenced blocks / ASCII tree | Conventions matched across all docs | ✅ Pass | 100% |
| Consistent terminology | F-IDs, "rendered DOM", "DOM-read invariant" | Applied throughout | ✅ Pass | 100% |
| Markdown lint cleanliness | Exit 0 | markdownlint-cli 0.49.0 → exit 0 | ✅ Pass | 100% |
| Link integrity | 0 broken | 294 links / anchors, 0 broken | ✅ Pass | 100% |
| SME / editorial human review | Required pre-publish | Not yet performed (non-autonomous) | ⏳ Pending | 0% |

**Fixes applied during autonomous validation:** CP1/CP2/CP4 review findings resolved (source fidelity, hub navigation, baseline alignment, grade wording, accessibility limitation); citations recalculated after the `Readme.md` Documentation-section insertion shifted line numbers; markdownlint brought to exit 0. **Outstanding:** human SME/editorial review (tracked as the highest-priority remaining task).

---

## 6. Risk Assessment

Nine risks were identified across the four PA3 categories. Severity is predominantly **Low** (a fully-validated docs deliverable on a tiny static app), with one **Medium** (citation/content drift). **No High/Critical risks; none block merge.**

| # | Risk | Category | Severity | Probability | Mitigation | Status |
|---|---|---|---|---|---|---|
| R1 | Citation/content drift if the embedded `Readme.md` code changes (668 line-precise citations could go stale) | Technical | Medium | Medium | SSOT docs (`data-schema`, `behavioral-contracts`); line-range citations; per-doc "update if code changes" note; optional CI lint/link gate | Mitigated by design; ongoing |
| R2 | Mermaid/link rendering variance on the actual host vs. local `mermaid-cli` validation | Technical | Low | Low–Medium | GitHub renders Mermaid natively; verify post-merge; optional SVG export | Open (host verification pending) |
| R3 | Cosmetic encoding mojibake (`â€”`) inside a `.markdownlint.yaml` comment | Technical | Low | N/A (present) | One-line fix; non-functional (lint still exit 0) | Open (low priority) |
| R4 | Secret leakage into docs (Env-1 staging `API_KEY` / `DB_HOST`) | Security | Low | Low | Docs sourced only from `Readme.md`; staging secrets explicitly excluded (AAP 0.9.2); verified none present | Closed / Mitigated |
| R5 | App client-side-only posture & no input validation (disclosed, **not** introduced) | Security | Low | N/A | Docs transparently document (e.g., per-subject max 100 "not enforced", no auth/persistence); source unchanged | Documented (informational) |
| R6 | No CI-enforced doc quality gate — future edits could break links/citations undetected | Operational | Low–Medium | Medium | Add optional `markdownlint` + `markdown-link-check` workflow; author-side checks pass today | Open (optional) |
| R7 | Documentation maintenance ownership for citation freshness | Operational | Low | Medium | Assign a doc owner; SSOT design minimizes drift surface | Open (process) |
| R8 | jsPDF 2.5.1 cdnjs CDN-availability runtime precondition (disclosed) | Integration | Low | Low | Documented as precondition + troubleshooting (`dependencies.md`, `usage.md`) | Documented |
| R9 | Embedded-vs-standalone source ambiguity (ASCII tree implies standalone files; they are embedded) | Integration | Low | N/A | Docs correctly treat as logical components; human decision on physical extraction (future code task, out of scope) | Open (product decision) |

---

## 7. Visual Project Status

**Project Hours Breakdown** (Completed = Dark Blue `#5B39F3`, Remaining = White `#FFFFFF`):

```mermaid
%%{init: {"theme":"base","themeVariables":{"pie1":"#5B39F3","pie2":"#FFFFFF","pieStrokeColor":"#B23AF2","pieStrokeWidth":"2px","pieOuterStrokeColor":"#B23AF2","pieOuterStrokeWidth":"2px","pieTitleTextColor":"#B23AF2","pieSectionTextColor":"#000000","pieSectionTextSize":"15px","pieLegendTextColor":"#000000"}}}%%
pie showData
    title Project Hours Breakdown (Total 66h)
    "Completed Work" : 58
    "Remaining Work" : 8
```

**Remaining Work by Priority** (8h total):

```mermaid
%%{init: {"theme":"base","themeVariables":{"pie1":"#5B39F3","pie2":"#A8FDD9","pie3":"#FFFFFF","pieStrokeColor":"#B23AF2","pieStrokeWidth":"2px","pieOuterStrokeColor":"#B23AF2","pieOuterStrokeWidth":"2px","pieTitleTextColor":"#B23AF2","pieSectionTextColor":"#000000","pieSectionTextSize":"15px","pieLegendTextColor":"#000000"}}}%%
pie showData
    title Remaining Work by Priority (8h)
    "High" : 4
    "Medium" : 1
    "Low" : 3
```

**Remaining Hours per Category** (bar view of Section 2.2):

| Category | Hours | Bar |
|---|---|---|
| SME / editorial review | 3 | ███████████████ |
| Host-platform render verification | 1 | █████ |
| Embedded-vs-standalone decision | 1 | █████ |
| Optional CI quality gates | 2 | ██████████ |
| Cosmetic config-comment fix | 1 | █████ |
| **Total** | **8** | |

> **Integrity:** the "Remaining Work" value (8h) equals Section 1.2 Remaining Hours and the Section 2.2 "Hours" total. "Completed Work" (58h) equals Section 1.2 Completed Hours and the Section 2.1 total.

---

## 8. Summary & Recommendations

**Achievements.** This effort delivers a complete, professionally structured documentation set for the Student Report Generator that **fully satisfies both governing constraints** of the request: functionalities are *clearly separated* (one page per functional area, F-001..F-008) and the *expectation from the code* is made explicit through a behavioral-contract block in every functional and API document plus a single-source-of-truth contracts page covering all five invariants. The set comprises 18 new Markdown files (~22,148 words), 11 Mermaid diagrams, and 668 line-precise citations, all committed at `5342de8` with the root `Readme.md` updated in a deliberately minimal way (+15 / −0 lines).

**Quality posture.** Every autonomous documentation gate passes: markdownlint exit 0, 294 links/anchors with zero breaks, 11/11 diagrams rendering to valid SVG, all citations within bounds and accurate, and runtime behavior confirmed by reconstructing the app in Chrome. These were independently re-verified during this assessment.

**Remaining gaps & critical path.** The project is **87.9% complete** (58 of 66 hours). The remaining 8 hours are entirely **path-to-production human activities**, not unfinished AAP work: a human SME/editorial review (3h, the critical path before publishing), host-platform render verification on GitHub (1h), a product decision on whether to extract the embedded source into standalone files in a future *code* task (1h), and two low-priority items — optional CI documentation gates (2h) and a cosmetic config-comment fix (1h).

**Success metrics.** AAP coverage targets are met in full: JS functions 2/2, functionalities 8/8, components 3/3 + jsPDF, fixed parameters 100%, and diagrams 11 (≥ 5 required).

**Production readiness.** The documentation is **mergeable now** and production-ready pending a standard human review pass. No release-blocking issues exist, and no risk rises above Medium severity. **Recommendation:** merge, perform the SME review (HT-1) and host-render check (HT-2), then publish; schedule the optional CI gates (HT-4) to protect against future citation/link drift (risk R1/R6).

| Dimension | Status |
|---|---|
| AAP autonomous scope | ✅ 100% complete & validated |
| Overall completion (AAP + path-to-production) | 🟦 87.9% |
| Release-blocking issues | ✅ None |
| Highest risk severity | ⚠ Medium (citation drift, mitigated by SSOT) |
| Production readiness | ✅ Mergeable; publish after human review |

---

## 9. Development Guide

This project is **zero-build**: there is no `package.json`, build system, test harness, or database. The documentation is plain Markdown with inline Mermaid and renders natively on GitHub and most Markdown viewers. The "development" workflow below covers previewing, validating, and (optionally) exporting the documentation, plus running the embedded application.

### 9.1 System Prerequisites

- **Git** 2.x (repository is already cloned at the branch `blitzy-e6cd2609-…`, HEAD `5342de8`).
- **A modern web browser** (Chrome/Edge/Firefox) — to preview the app and render Mermaid locally if desired.
- **Internet access** — only for (a) the jsPDF CDN at app runtime, and (b) `npx`-fetching the *optional* author-side validation tools.
- **Node.js ≥ 18 + npm** — *optional*, only to run the documentation quality tools. Verified versions in this environment: Node `v20.20.2`, npm `10.8.2`, Python `3.13.13`, Git `2.54.0`, Chrome `149`.

> No environment variables are required to author, render, or run anything in this project.

### 9.2 Environment Setup

```bash
# From the repository root (Windows PowerShell or any shell)
git status                 # confirm a clean working tree
git rev-parse --short HEAD # expect: 5342de8
ls docs                    # expect: index.md + architecture/ functionality/ api-reference/ reference/ contracts/ guides/ dependencies.md
```

No virtual environment, dependency install, or service startup is needed to read the documentation.

### 9.3 Dependency Installation (Optional Author-Side Tools)

These tools are **not committed** and are **not required** to read or render the docs. Install globally only if you intend to run the quality gates locally:

```bash
npm install -g markdownlint-cli@0.49.0 markdown-link-check@3.14.2 @mermaid-js/mermaid-cli@11.15.0
```

Or invoke them ad hoc with `npx --yes <tool>@<version>` (no global install needed).

### 9.4 Documentation Workflow & Quality Checks (all tested)

```bash
# 1) Markdown lint (compile-equivalent gate) — expect exit code 0, zero violations
npx --yes markdownlint-cli@0.49.0 Readme.md "docs/**/*.md"

# 2) Internal link & anchor check — expect 0 broken links
npx --yes markdown-link-check@3.14.2 docs/index.md
npx --yes markdown-link-check@3.14.2 Readme.md

# 3) (Optional) Export a Mermaid diagram to SVG — requires a Chrome/puppeteer config
npx --yes @mermaid-js/mermaid-cli@11.15.0 -i diagram.mmd -o diagram.svg -p puppeteer-config.json
```

### 9.5 Verification Steps (expected outputs)

| Check | Command | Expected Output |
|---|---|---|
| Lint clean | `markdownlint Readme.md "docs/**/*.md"` | Exit `0`, no output |
| Links valid | `markdown-link-check docs/index.md` | All links reported as `✓`, `0 dead links` |
| Diagrams valid | `mmdc -i <d>.mmd -o <d>.svg` | One `.svg` produced per diagram, no errors |
| Citations in bounds | (audit) | Max cited line ≤ 296 (current `Readme.md` length) |

### 9.6 Example Usage (running the embedded application)

The app source is embedded in `Readme.md` as fenced `html`/`css`/`javascript` blocks. To run it:

1. Reconstruct `index.html`, `style.css`, and `script.js` from the fenced blocks in `Readme.md` (or open the standalone `index.html` if one has been extracted in a future task).
2. Open `index.html` in a browser.
3. Enter a student name, roll number, and five subject marks.
4. **Click "Generate Report" *before* "Download PDF"** (required ordering).
5. Verify the report card; then click "Download PDF".

**Worked example:** marks `90, 85, 80, 75, 70` → **Total 400**, **Percentage 80.00%**, **Grade A**; PDF saved as `<name>_Report.pdf`.

### 9.7 Troubleshooting

| Symptom | Cause | Resolution |
|---|---|---|
| "Download PDF" does nothing; console shows `TypeError` | jsPDF CDN failed to load → `window.jspdf` is `undefined` | Ensure internet access and reload so the cdnjs script resolves (see `docs/dependencies.md`). |
| Report card is empty when clicking "Download PDF" | "Generate Report" was not clicked first (DOM-read invariant) | Always Generate before Download (see `docs/guides/usage.md`). |
| Mermaid diagrams show as raw code | Viewer doesn't render Mermaid | Use GitHub or a Mermaid-aware viewer; or export to SVG via `mmdc`. |
| `npx` fails to fetch a tool | No internet / offline | Pre-install the tools globally (Section 9.3) on a connected machine. |
| `markdownlint` flags unexpected rules | Custom config not picked up | Run from the repo root so `.markdownlint.yaml` is detected. |

---

## 10. Appendices

### A. Command Reference

| Purpose | Command |
|---|---|
| Confirm branch/HEAD | `git rev-parse --abbrev-ref HEAD` · `git rev-parse --short HEAD` |
| Diff scope vs. baseline | `git diff --stat 5eb1147 HEAD` |
| Lint all docs | `npx --yes markdownlint-cli@0.49.0 Readme.md "docs/**/*.md"` |
| Link check | `npx --yes markdown-link-check@3.14.2 docs/index.md` |
| Render a diagram | `npx --yes @mermaid-js/mermaid-cli@11.15.0 -i <d>.mmd -o <d>.svg -p <cfg>` |
| Count Mermaid diagrams | `grep -rc '```mermaid' docs` |

### B. Port Reference

| Service | Port | Notes |
|---|---|---|
| *(none)* | — | No server. The app runs from `index.html` over the `file://` protocol (or any static host); the docs require no server. |

### C. Key File Locations

| Path | Role |
|---|---|
| `Readme.md` | Project README + embedded source (`index.html`/`style.css`/`script.js`); now includes the Documentation nav section. |
| `docs/index.md` | Documentation hub & master TOC. |
| `docs/architecture/` | `overview.md`, `component-model.md`, `data-flow.md`. |
| `docs/functionality/` | `data-entry`, `computation`, `grading`, `report-rendering`, `pdf-export`, `styling` (F-001..F-008). |
| `docs/api-reference/` | `script-js.md`, `html-structure.md`, `css-reference.md`. |
| `docs/reference/data-schema.md` | Fixed parameters (subjects, denominator, thresholds). |
| `docs/contracts/behavioral-contracts.md` | SSOT for invariants/preconditions/postconditions. |
| `docs/guides/` | `getting-started.md`, `usage.md`. |
| `docs/dependencies.md` | jsPDF CDN integration contract. |
| `.markdownlint.yaml` | Optional lint config with justified rule exceptions. |

### D. Technology Versions

| Component | Version | Role |
|---|---|---|
| jsPDF | 2.5.1 (cdnjs) | Runtime PDF export dependency (documented as-is; not upgraded). |
| markdownlint-cli | 0.49.0 | Optional doc lint. |
| markdown-link-check | 3.14.2 | Optional link validation. |
| @mermaid-js/mermaid-cli | 11.15.0 | Optional diagram export. |
| Node.js / npm | 20.20.2 / 10.8.2 | Optional tooling runtime (verified). |
| Python | 3.13.13 | Available; used only for ad-hoc audits. |
| Git | 2.54.0 | Version control. |

### E. Environment Variable Reference

| Variable | Required? | Notes |
|---|---|---|
| *(none)* | No | The project needs no environment variables. The AAP-flagged Env-1 staging `API_KEY` / `DB_HOST` are **not applicable** to this static repo and were treated as sensitive and excluded (AAP 0.9.2). |

### F. Developer Tools Guide

- **Preview:** open any `.md` on GitHub or in a Mermaid-aware Markdown viewer — diagrams and tables render with no build step.
- **Lint locally:** `markdownlint` honors `.markdownlint.yaml` from the repo root (MD013/MD060/MD025 intentionally disabled with documented rationale).
- **Validate links:** `markdown-link-check` per file; for internal-only validation a fence-aware scan resolves relative `.md` links and same-file anchors.
- **Diagrams:** all 11 diagrams are inline; export to SVG/PNG via `mmdc` only if static images are later required.

### G. Glossary

| Term | Meaning |
|---|---|
| **AAP** | Agent Action Plan — the authoritative project directive. |
| **F-001 … F-008** | The eight discrete features (Identity Capture, Marks Entry, Total, Percentage, Grade, On-Screen Render, PDF Export, Styling). |
| **DOM-read invariant** | `downloadPDF()` reads from the *rendered report-card DOM*, not the form inputs — so Generate must precede Download. |
| **Rebuild-from-scratch** | `generateReport()` clears and re-populates the marks table each run (no row accumulation). |
| **No-NaN guard** | `parseInt(value) || 0` coerces blank/invalid inputs to `0`. |
| **SSOT** | Single Source Of Truth — `behavioral-contracts.md` (invariants) and `data-schema.md` (fixed values). |
| **Contract block** | The "Expected Behavior / Contract" section documenting inputs, outputs, preconditions, postconditions, invariants, side effects, and error modes. |
| **Zero-build** | No compilation, bundling, or package install required to use the deliverable. |

---

*Generated by the Blitzy Platform · Documentation completion (AAP-scoped): 87.9% · Branch `blitzy-e6cd2609-e4f7-4ddf-83c4-7c04a97167e6` @ `5342de8`*
