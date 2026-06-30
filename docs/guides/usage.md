# Usage Guide

## Purpose

This guide walks you through using the **Student Report Generator** end-to-end — entering a student's details, generating the on-screen report card, and exporting it as a PDF. It states the one **critical workflow-ordering rule** (you must click **Generate Report** before **Download PDF**) and provides a **Troubleshooting** section for the issues you are most likely to hit.

If the application is not open yet, start with [`getting-started.md`](getting-started.md) for prerequisites and zero-install run steps, then return here.

Everything in this guide is **code-grounded**: every behavioral claim is traceable to the application source embedded in the repository-root `Readme.md` and is cited inline as `[Readme.md:Lx-Ly]`.

---

## Feature Walkthrough

The Student Report Generator is driven by a single form — name, roll number, and five subject inputs [Readme.md:L46-L53] — plus two action buttons, **Generate Report** and **Download PDF** [Readme.md:L55-L56]. A complete session is three steps:

1. **Enter identity and marks.** Type the student's **name** and **roll number**, then the five subject marks — **Maths, Science, English, History, Computer** — into the **form inputs** at the top of the page [Readme.md:L46-L53]. Blank (falsy) mark values default to `0` (the **no-NaN guard**, explained under [Troubleshooting](#troubleshooting)); the code performs no other input validation. See [`../functionality/data-entry.md`](../functionality/data-entry.md) for identity capture (**F-001**) and marks entry (**F-002**).

2. **Click "Generate Report".** This button is wired to `generateReport()` [Readme.md:L55]. It sums the five subject marks into a total and computes the percentage as `(total / 500) * 100` [Readme.md:L174-L192] (see [`../functionality/computation.md`](../functionality/computation.md), **F-003** / **F-004**), assigns a letter grade from that percentage [Readme.md:L194-L206] (see [`../functionality/grading.md`](../functionality/grading.md), **F-005**), and renders the on-screen **report card** — name, roll number, a five-row marks table, total, percentage (to two decimals), and grade [Readme.md:L176-L212] (see [`../functionality/report-rendering.md`](../functionality/report-rendering.md), **F-006**).

3. **Click "Download PDF".** This button is wired to `downloadPDF()` [Readme.md:L56]. It reads the values from the **rendered** report card and saves them to a PDF file named `<name>_Report.pdf` [Readme.md:L215-L237] (see [`../functionality/pdf-export.md`](../functionality/pdf-export.md), **F-007**).

### Illustrative worked example

The marks below are **illustrative** and use the same values as the other documents for cross-document consistency; the full grade derivation is owned by [`../functionality/grading.md`](../functionality/grading.md).

| Subject | Marks |
| --- | --- |
| Maths | 90 |
| Science | 85 |
| English | 80 |
| History | 75 |
| Computer | 70 |

Entering these marks and clicking **Generate Report** produces a **total** of `90 + 85 + 80 + 75 + 70 = 400`, a **percentage** of `(400 / 500) * 100 = 80` displayed as `"80.00"` [Readme.md:L192] [Readme.md:L211], and a **grade** of **A** (a percentage of `80` satisfies `≥ 80`) [Readme.md:L194-L206]. Clicking **Download PDF** then saves `<name>_Report.pdf` with those rendered values [Readme.md:L236].

---

## Workflow Ordering (Critical)

**You must click "Generate Report" before "Download PDF".**

The reason is grounded in the code: `downloadPDF()` reads its values from the **rendered report-card DOM** — the spans `#rName`, `#rRoll`, `#totalMarks`, `#percentage`, and `#grade` — and **not** from the **form inputs** [Readme.md:L220-L224]:

```javascript
const name = document.getElementById('rName').innerText;
const roll = document.getElementById('rRoll').innerText;
```

This is the **DOM-read invariant**: the exported PDF mirrors whatever the **rendered DOM** currently shows. It is `generateReport()` that populates those rendered spans [Readme.md:L208-L212]. Therefore, if you click **Download PDF** before **Generate Report**, the report card has not been populated yet, and the saved PDF contains **blank values** — no error is thrown, because nothing in the code enforces the order. The same applies if you edit the form after generating: re-click **Generate Report** to refresh the **rendered DOM** before downloading.

For the authoritative statement of this invariant and the ordering precondition, see [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md); for the export reference, see [`../functionality/pdf-export.md`](../functionality/pdf-export.md).

---

## Troubleshooting

Each item below describes an **expected behavior** of the code (a documented expectation, not a defect to fix in code). For the authoritative invariants and fixed values, follow the linked single-source documents.

| Symptom | Cause | Fix |
| --- | --- | --- |
| **PDF is blank, or shows old data.** | You did not click **Generate Report** first, or you changed the form after generating and did not regenerate. `downloadPDF()` reads the **rendered report-card DOM**, not the **form inputs** — the **DOM-read invariant** [Readme.md:L220-L224]. | Click **Generate Report**, then **Download PDF**; re-generate after any form change. See [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md). |
| **Download does nothing; the browser console shows a `TypeError`.** | The jsPDF CDN script failed to load (offline, firewall, or CDN outage), so `window.jspdf` is `undefined` and `const { jsPDF } = window.jspdf;` throws [Readme.md:L38] [Readme.md:L216]. There is **no in-app error message** — there is no `try`/`catch`, so the failure surfaces only in the browser console. | Ensure you have internet access and reload the page so the CDN resolves. See [`../dependencies.md`](../dependencies.md). |
| **A subject's marks are treated as `0`.** | A blank or otherwise **falsy** mark `.value` defaults to `0` via the **no-NaN guard** `parseInt(document.getElementById('<id>').value \|\| 0)` [Readme.md:L167-L171]: a blank number field has `.value === ""` (falsy), which becomes `0` *before* `parseInt` runs, so the field contributes `0` rather than `NaN`. The guard handles only blank/falsy input; there is no general non-numeric validation. | Enter a numeric value for every subject you intend to count. See [`../functionality/data-entry.md`](../functionality/data-entry.md). |
| **The percentage seems too low.** | The denominator is **fixed at 500** (five subjects × 100), so any subject left blank counts as `0` and lowers the result — `percentage = (total / 500) * 100` [Readme.md:L192]. This is expected, not a bug. | Fill in all five subjects, or read the score as always out of 500. See [`../reference/data-schema.md`](../reference/data-schema.md). |

The `marks → 0` behavior above is the **no-NaN guard** in action — note that the `|| 0` is applied to `.value` *inside* `parseInt` [Readme.md:L167-L171]:

```javascript
Maths: parseInt(document.getElementById('maths').value || 0),
```

---

## Related Documents

- [`getting-started.md`](getting-started.md) — prerequisites and zero-install run steps (start here if the app is not open yet).
- [`../functionality/data-entry.md`](../functionality/data-entry.md) — identity capture (**F-001**) and marks entry (**F-002**), including the no-NaN guard.
- [`../functionality/computation.md`](../functionality/computation.md) — total aggregation (**F-003**) and the percentage formula (**F-004**).
- [`../functionality/grading.md`](../functionality/grading.md) — grade assignment (**F-005**) and the grade-decision flowchart.
- [`../functionality/report-rendering.md`](../functionality/report-rendering.md) — on-screen report-card rendering (**F-006**).
- [`../functionality/pdf-export.md`](../functionality/pdf-export.md) — PDF export (**F-007**) and the DOM-read invariant.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — invariants and preconditions (DOM-read invariant, ordering, jsPDF-present).
- [`../reference/data-schema.md`](../reference/data-schema.md) — fixed subjects, the 500-point denominator, and the grade thresholds.
- [`../index.md`](../index.md) — back to the Documentation Hub.
