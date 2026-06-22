# Usage Guide

## Purpose

This guide walks through using the **Student Report Generator** end-to-end — entering data,
generating the on-screen report card, and exporting it as a PDF. It states the one CRITICAL
workflow-ordering rule (**Generate Report before Download PDF**) and provides a troubleshooting
section for the most common surprises. All content on this page is code-grounded in the application
source embedded in the repository-root `Readme.md`, and every technical claim carries an inline
`[Readme.md:Lx-Ly]` citation.

If this is your first time running the app, start with [`getting-started.md`](getting-started.md)
for the prerequisites and zero-install run steps, then return here for the walkthrough.

---

## Feature Walkthrough

The application is a single screen: a form at the top and a report card below it. The end-to-end
flow is three steps, each backed by a separately documented functionality.

1. **Enter identity and marks.** Type the student name and roll number, then the five subject marks
   (Maths, Science, English, History, Computer) into the **form inputs** [Readme.md:L46-L53]. See
   [`../functionality/data-entry.md`](../functionality/data-entry.md) for identity capture (F-001)
   and marks entry (F-002), including the **no-NaN guard** that turns a blank mark into `0`.

2. **Click "Generate Report".** This button is wired to `generateReport()` [Readme.md:L55]. It sums
   the five marks into a total and computes the percentage against a fixed 500-point denominator
   [Readme.md:L174-L192] (see [`../functionality/computation.md`](../functionality/computation.md),
   F-003/F-004), assigns a letter grade from that percentage [Readme.md:L194-L206] (see
   [`../functionality/grading.md`](../functionality/grading.md), F-005), and renders the on-screen
   report card — name, roll number, a rebuilt marks table, total, percentage (two decimals), and
   grade [Readme.md:L176-L212] (see
   [`../functionality/report-rendering.md`](../functionality/report-rendering.md), F-006).

3. **Click "Download PDF".** This button is wired to `downloadPDF()` [Readme.md:L56]. It exports the
   **rendered** report card as a PDF file named `<name>_Report.pdf` [Readme.md:L215-L237] (see
   [`../functionality/pdf-export.md`](../functionality/pdf-export.md), F-007).

### Illustrative worked example

The sample below is illustrative; the grade rubric is owned by
[`../functionality/grading.md`](../functionality/grading.md) and
[`../reference/data-schema.md`](../reference/data-schema.md).

| Subject | Marks |
|---|---|
| Maths | 90 |
| Science | 85 |
| English | 80 |
| History | 75 |
| Computer | 70 |
| **Total** | **400** |

With these marks the total is `400`, the percentage is `(400 / 500) * 100 = 80.00`
[Readme.md:L192], and because `80 >= 80` (and `< 90`) the grade is **A** [Readme.md:L194-L206].

---

## Workflow Ordering (Critical)

**You must click "Generate Report" before "Download PDF".**

The reason is structural and grounded in the code: `downloadPDF()` reads its values from the
**rendered DOM** — the report-card spans `#rName`, `#rRoll`, `#totalMarks`, `#percentage`, and
`#grade` — and **not** from the **form inputs** [Readme.md:L220-L224]. This is the
**DOM-read invariant**:

```javascript
const name = document.getElementById('rName').innerText;
const total = document.getElementById('totalMarks').innerText;
```

Those spans are populated only when `generateReport()` writes the results back to the DOM
[Readme.md:L208-L212]. If you click **Download PDF** first, the report card has never been
populated, so the exported PDF contains blank values. The same applies if you edit the form after
generating: the rendered spans still hold the previous result until you click **Generate Report**
again.

For the authoritative statement of this invariant and the ordering precondition, see
[`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md); for the export
behavior itself, see [`../functionality/pdf-export.md`](../functionality/pdf-export.md).

---

## Troubleshooting

Each item below is an expected consequence of how the code is written — a documented behavior, not a
defect to fix in code.

| Symptom | Cause | Fix |
|---|---|---|
| **PDF is blank, or shows old data.** | You didn't click **Generate Report** first, or you edited the form after generating and didn't regenerate. `downloadPDF()` reads the **rendered DOM**, not the **form inputs** [Readme.md:L220-L224] (the **DOM-read invariant**). | Click **Generate Report**, then **Download PDF**; regenerate after any edit. See [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md). |
| **Download does nothing; the browser console shows a `TypeError`.** | The jsPDF CDN script failed to load (offline, firewall, or CDN outage), so `window.jspdf` is `undefined` and the line `const { jsPDF } = window.jspdf;` throws [Readme.md:L38, L216]. The function has no `try`/`catch` and no programmatic error handling [Readme.md:L215-L237], so the error surfaces only in the console — there is no in-app message. | Ensure internet access and reload the page so the CDN script resolves. See [`../dependencies.md`](../dependencies.md). |
| **A subject's marks are treated as `0`.** | A blank or non-numeric mark defaults to `0` via the **no-NaN guard** applied to each subject's `.value` (verbatim form shown below the table) — a blank `.value` is the empty string (falsy), so it becomes `0` *before* `parseInt` runs, never `NaN` [Readme.md:L167-L171]. | Enter a numeric value for every subject you intend to count. See [`../functionality/data-entry.md`](../functionality/data-entry.md). |
| **The percentage seems low.** | The denominator is fixed at **500** (five subjects × 100), so any subject left blank counts as `0` and lowers the result: `(total / 500) * 100` [Readme.md:L192]. | Fill all five subjects, or read the score as always being out of 500. See [`../reference/data-schema.md`](../reference/data-schema.md). |

The **no-NaN guard**, verbatim from the source, wraps each subject's `.value` so a blank field
becomes `0` rather than `NaN` [Readme.md:L167-L171]:

```javascript
Maths: parseInt(document.getElementById('maths').value || 0),
```

This is a documented expectation of the code, not a range check: the guard prevents `NaN`, but it
does **not** clamp negatives or cap a mark at 100. See
[`../reference/data-schema.md`](../reference/data-schema.md) and
[`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) for the full schema
and invariants.

---

## Related Documents

- [`getting-started.md`](getting-started.md) — prerequisites and the zero-install run steps.
- [`../functionality/data-entry.md`](../functionality/data-entry.md) — identity capture (F-001) and
  marks entry (F-002), including the no-NaN guard.
- [`../functionality/computation.md`](../functionality/computation.md) — total (F-003) and
  percentage (F-004) computation.
- [`../functionality/grading.md`](../functionality/grading.md) — grade assignment (F-005) and the
  threshold rubric.
- [`../functionality/report-rendering.md`](../functionality/report-rendering.md) — on-screen
  report-card rendering (F-006).
- [`../functionality/pdf-export.md`](../functionality/pdf-export.md) — PDF export (F-007) and the
  DOM-read invariant.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — invariants and
  preconditions (DOM-read invariant, workflow ordering, jsPDF-present).
- [`../reference/data-schema.md`](../reference/data-schema.md) — fixed subjects, the 500-point
  denominator, and the grade thresholds.
- [`../index.md`](../index.md) — back to the Documentation Hub.
