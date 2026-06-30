# Report Rendering

> **Feature F-006 — On-Screen Rendering (Presentation layer).** This guide documents how `generateReport()` writes its computed results into the on-screen report-card DOM, including the **rebuild-from-scratch** marks table.

## Purpose

This document describes **F-006 On-Screen Rendering**: the stage of `generateReport()` in which the computed results are written into the **rendered DOM** report card, and the marks table is rebuilt from scratch with one row per subject [Readme.md:L176-L212]. It is code-grounded — every claim cites the application source embedded in the repository-root `Readme.md`, which is the sole source of truth for this project.

## Source Location

- **Report-card markup (the rendered region and its placeholders)** — the embedded `index.html` block [Readme.md:L59-L78]
- **Rendering logic (table rebuild + result writes)** — the tail of `generateReport()` in the embedded `script.js` block [Readme.md:L176-L212]

---

## F-006 On-Screen Rendering

### The report-card region

The application renders results into a single static container, `#reportCard` [Readme.md:L59]:

```html
<div id="reportCard" class="report-card">
```

Inside it are the **report-card placeholders** that `generateReport()` fills: the identity spans `#rName` [Readme.md:L62] and `#rRoll` [Readme.md:L63]; the result spans `#totalMarks` [Readme.md:L75], `#percentage` [Readme.md:L76], and `#grade` [Readme.md:L77]; and the marks-table body `<tbody id="marksTable">` [Readme.md:L72]:

```html
<tbody id="marksTable"></tbody>
```

These elements are empty in the static markup and remain empty until `generateReport()` runs [Readme.md:L59-L78]. For the complete element/ID reference — every input and report-card ID with its type and wiring — see [`../api-reference/html-structure.md`](../api-reference/html-structure.md); this guide does not duplicate that table.

### Building the marks table

The rendering stage first fetches the marks-table body and **clears it**, then re-appends one row per subject inside the `for...in` loop [Readme.md:L176-L190]:

```javascript
const tableBody = document.getElementById('marksTable');
tableBody.innerHTML = '';
```

The loop iterates the five-subject object and appends one `<tr>` per subject [Readme.md:L179-L190]:

```javascript
tableBody.innerHTML += row;
```

(The same loop also accumulates the running `total`; that aggregation belongs to computation and is documented in [`computation.md`](computation.md).)

Two precise readings matter here:

- Only the **`<tbody>`** rows are rebuilt. `#marksTable` *is* the `<tbody>` element [Readme.md:L72], so clearing and appending to it affects only the data rows.
- The enclosing `<table>` and its `<thead>` header row (columns **Subject** and **Marks**) are **static markup** and are never touched by the code [Readme.md:L65-L71].

### Writing the result fields

After the loop — once the percentage and grade have been computed — `generateReport()` writes the five report-card placeholders [Readme.md:L208-L212]:

```javascript
document.getElementById('rName').innerText = name;
document.getElementById('rRoll').innerText = roll;
document.getElementById('totalMarks').innerText = total;
document.getElementById('percentage').innerText = percentage.toFixed(2);
document.getElementById('grade').innerText = grade;
```

Note the formatting asymmetry between the two numeric fields:

- `#totalMarks` is written **unformatted** — the raw numeric `total` [Readme.md:L210].
- `#percentage` is written as a **2-decimal string** via `.toFixed(2)` [Readme.md:L211]. (The underlying percentage number is produced by the computation feature; see [`computation.md`](computation.md).)

The literal `%` sign that appears after the percentage is **static markup**, not written by the code [Readme.md:L76]:

```html
<p><strong>Percentage:</strong> <span id="percentage"></span>%</p>
```

The JavaScript writes only the value of `#percentage`; the `%` already lives in the surrounding `<p>` element.

---

## Rebuild-From-Scratch Invariant

The defining expectation of this feature is **rebuild-from-scratch** rendering. Each time `generateReport()` runs, it **clears** the marks-table body before repopulating it [Readme.md:L177]:

```javascript
tableBody.innerHTML = '';
```

Because the `<tbody>` is wiped first and then exactly one `<tr>` is appended per subject in the loop [Readme.md:L176-L190], the rendered table always contains **exactly five fresh rows** (one per subject) and **never accumulates stale rows** from previous runs. The report card therefore always reflects the **most recent** Generate Report action.

Without this clear step, repeated clicks of **Generate Report** would append duplicate rows on top of the previous ones; the clear is what keeps the table render idempotent across runs.

This invariant is owned by the project's behavioral-contracts document, which is the single source of truth for it — see [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md). The fixed five-subject schema that determines the table's row count, together with the `500`-point total/denominator, is owned by [`../reference/data-schema.md`](../reference/data-schema.md) (the single source of truth for fixed values).

---

## Expected Behavior / Contract

| Aspect | Expectation | Source |
| --- | --- | --- |
| **Trigger** | Executes inside `generateReport()` — the table rebuild runs as part of the subject loop; the five span writes run at the end, after the percentage and grade are computed. | [Readme.md:L176-L212] |
| **Writes — `#rName`** | Set to the student name via `innerText`. | [Readme.md:L208] |
| **Writes — `#rRoll`** | Set to the roll number via `innerText`. | [Readme.md:L209] |
| **Writes — `#totalMarks`** | Set to the raw numeric `total`, **unformatted**. | [Readme.md:L210] |
| **Writes — `#percentage`** | Set to a **2-decimal string** via `.toFixed(2)`. | [Readme.md:L211] |
| **Writes — `#grade`** | Set to the assigned grade string. | [Readme.md:L212] |
| **Writes — `#marksTable`** | `<tbody>` rebuilt to **exactly five rows**, one `<tr>` per subject. | [Readme.md:L176-L190] |
| **Rebuild-from-scratch invariant** | The `<tbody>` is cleared before repopulation, so no stale rows survive across runs; the report card reflects the latest inputs. | [Readme.md:L177] |
| **Preconditions** | The six report-card IDs — `#rName`, `#rRoll`, `#marksTable`, `#totalMarks`, `#percentage`, `#grade` — must exist exactly as spelled. A missing ID makes `document.getElementById(...)` return `null`, and the subsequent write throws a `TypeError` (no programmatic handling; defer to the contracts document). | [Readme.md:L176], [Readme.md:L208-L212] |
| **Idempotency / determinism** | Re-running with identical form inputs produces an identical report card — the same five rows and the same total, percentage, and grade. | [Readme.md:L176-L212] |
| **Side effects** | Mutates the **rendered DOM** only — no persistence and no network. `#reportCard` is **always present** in the DOM; the code never hides, shows, or toggles it. | [Readme.md:L59-L78] |
| **Downstream (DOM-read) note** | This rendered output is exactly what `downloadPDF()` later reads (the **DOM-read invariant**), which is why Generate Report must run before Download PDF. | [Readme.md:L220-L224] |

The required-IDs precondition, the rebuild-from-scratch invariant, and the DOM-read invariant are all defined canonically in [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md); the PDF-export consumer is documented in [`pdf-export.md`](pdf-export.md).

---

## Related Documents

- [`../api-reference/html-structure.md`](../api-reference/html-structure.md) — the full report-card element/ID reference and function wiring.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the single source of truth for the rebuild-from-scratch invariant, the required-IDs precondition, and the DOM-read invariant.
- [`../reference/data-schema.md`](../reference/data-schema.md) — the single source of truth for the fixed five-subject schema (the table's five rows) and the `500`-point total/denominator.
- [`computation.md`](computation.md) and [`grading.md`](grading.md) — the sources of the rendered `total`, `percentage`, and `grade` values.
- [`pdf-export.md`](pdf-export.md) — the downstream consumer (F-007) that reads this rendered DOM (the DOM-read invariant).
- [`../index.md`](../index.md) — back to the Documentation Hub.

---

*Maintenance note: this guide is derived from the application source embedded in `Readme.md` [Readme.md:L59-L78], [Readme.md:L176-L212]. If that embedded code changes, update the cited line ranges and statements here so this document stays accurate.*
