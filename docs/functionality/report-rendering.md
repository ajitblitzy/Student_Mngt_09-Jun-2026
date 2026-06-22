# Report Rendering

## Purpose

This guide documents feature **F-006 (On-Screen Rendering)** of the Presentation layer: how
`generateReport()` writes its computed results into the on-screen **report-card** DOM region,
including the **rebuild-from-scratch** marks table that is cleared and repopulated on every run.
Like every page in this documentation set, it is code-grounded — every technical claim carries an
inline `[Readme.md:Lx-Ly]` citation back to the application source embedded in the
repository-root `Readme.md`.

---

## Source Location

- **Report-card DOM region (static markup):** [Readme.md:L74-L93]
- **Rendering logic (marks-table rebuild + result writes):** [Readme.md:L191-L227]

The markup that receives the rendered output lives in the `index.html` block [Readme.md:L74-L93],
and the JavaScript that populates it runs at the end of `generateReport()` in the `script.js`
block [Readme.md:L191-L227].

---

## F-006 On-Screen Rendering

On-screen rendering is the final stage of `generateReport()`: after the **form inputs** are read
and the `total`, `percentage`, and `grade` are computed, the function writes those results into
the **report-card** region of the page [Readme.md:L191-L227].

### The report-card region

The report card is a single container `<div id="reportCard">` that is **always present** in the
page markup [Readme.md:L74]. Inside it are the **report-card placeholders** that JavaScript fills
in — five `<span>` elements (`#rName`, `#rRoll`, `#totalMarks`, `#percentage`, `#grade`) and a
single `<tbody id="marksTable">` for the per-subject rows [Readme.md:L77-L92]:

```html
<div id="reportCard" class="report-card">
    ...
    <tbody id="marksTable"></tbody>
    ...
    <p><strong>Percentage:</strong> <span id="percentage"></span>%</p>
```

Everything else in the region is **static markup**: the `<h2>Student Report</h2>` heading
[Readme.md:L75], the surrounding `<table>` and its `<thead>` column headers `Subject` and `Marks`
[Readme.md:L80-L86], and the literal `%` sign printed after `#percentage` [Readme.md:L91]. For the
complete element/ID reference, see
[`../api-reference/html-structure.md`](../api-reference/html-structure.md).

### Building the marks table

The marks table is built in three steps. `generateReport()` first fetches the `<tbody>` by its ID
[Readme.md:L191], then **clears it** by assigning an empty string to `innerHTML` [Readme.md:L192],
and finally appends one `<tr>` row per subject inside the `for...in` loop [Readme.md:L194-L205]:

```javascript
const tableBody = document.getElementById('marksTable');  // L191
tableBody.innerHTML = '';                                  // L192 — clear (rebuild-from-scratch)
// ...
tableBody.innerHTML += row;                                // L204 — append one <tr> per subject
```

Because the `subjects` object holds exactly five keys [Readme.md:L181-L187], the loop appends
**exactly five rows** — one per subject — into the otherwise-static `<table>` [Readme.md:L194-L205].
Only the `<tbody id="marksTable">` content is rebuilt; the `<table>` and `<thead>` markup
(including the `Subject`/`Marks` headers) are never touched by JavaScript [Readme.md:L80-L87].

### Writing the result fields

After the table is rebuilt, the five report-card spans are written in a single pass
[Readme.md:L223-L227]:

```javascript
document.getElementById('rName').innerText = name;                       // L223
document.getElementById('rRoll').innerText = roll;                       // L224
document.getElementById('totalMarks').innerText = total;                 // L225 — unformatted
document.getElementById('percentage').innerText = percentage.toFixed(2); // L226 — 2-decimal string
document.getElementById('grade').innerText = grade;                      // L227
```

Two precision details matter here:

- `#totalMarks` is written **unformatted** — the raw numeric `total` is assigned directly to
  `.innerText` [Readme.md:L225].
- `#percentage` is written as a **two-decimal string** via `.toFixed(2)` [Readme.md:L226]; the
  trailing `%` the reader sees is the static markup at [Readme.md:L91], **not** produced by this
  write.

The `total`, `percentage`, and `grade` values themselves are produced upstream in the same
function — see [`computation.md`](computation.md) for `total` and `percentage`, and
[`grading.md`](grading.md) for `grade`.

---

## Rebuild-From-Scratch Invariant

The defining expectation of F-006 is **rebuild-from-scratch** rendering. Each `generateReport()`
run **clears** the marks-table body with `tableBody.innerHTML = ''` [Readme.md:L192] *before* the
per-subject loop repopulates it [Readme.md:L194-L205]. As a result, the rendered table always
contains **exactly five fresh rows** (one per subject) and **never accumulates stale or duplicate
rows** from previous runs [Readme.md:L191-L205].

Without the clear at [Readme.md:L192], repeated **Generate Report** clicks would append duplicate
rows on top of the previous ones; with it, the report card always reflects the **most recent**
Generate Report action. This invariant is owned by the behavioral-contracts document — see
[`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md#1-rebuild-from-scratch-rendering)
as the single source of truth.

---

## Expected Behavior / Contract

| Aspect | Expectation | Source |
|---|---|---|
| **Trigger** | Runs at the end of `generateReport()`, after `total`, `percentage`, and `grade` are computed. | [Readme.md:L191-L227] |
| **Writes — name** | `#rName.innerText = name` (the student name). | [Readme.md:L223] |
| **Writes — roll** | `#rRoll.innerText = roll` (the roll number). | [Readme.md:L224] |
| **Writes — total** | `#totalMarks.innerText = total`, written **unformatted**. | [Readme.md:L225] |
| **Writes — percentage** | `#percentage.innerText = percentage.toFixed(2)`, a **two-decimal string**; the trailing `%` is static markup. | [Readme.md:L226], [Readme.md:L91] |
| **Writes — grade** | `#grade.innerText = grade`. | [Readme.md:L227] |
| **Writes — marks table** | `<tbody id="marksTable">` rebuilt to **exactly five rows**, one per subject. | [Readme.md:L191-L205] |
| **Rebuild-from-scratch** | The table body is cleared before repopulation → no stale rows across runs; the report card reflects the latest inputs. | [Readme.md:L192] |
| **Determinism** | Re-running with identical **form inputs** produces an identical report card (no randomness, time, or persistence). | [Readme.md:L177-L228] |
| **Side effects** | Mutates the **rendered DOM** only — no persistence, no network; `#reportCard` is always present and is never hidden or shown. | [Readme.md:L74-L93], [Readme.md:L223-L227] |

**Preconditions.** The six report-card IDs — `#rName`, `#rRoll`, `#marksTable`, `#totalMarks`,
`#percentage`, `#grade` — must exist exactly as spelled in the markup [Readme.md:L77-L92]; the full
reference is in [`../api-reference/html-structure.md`](../api-reference/html-structure.md). If any
ID is absent, `document.getElementById(...)` returns `null` and the subsequent write throws a
`TypeError`; the code has **no guard** for this — defer to the error modes in
[`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md#error-modes).

**Downstream note (DOM-read invariant).** The report card this feature renders is exactly what
`downloadPDF()` later reads — it pulls its values from the **rendered DOM**, not from the
**form inputs** [Readme.md:L235-L239]. This is why **Generate Report must run before Download PDF**.
See [`pdf-export.md`](pdf-export.md) and the
[DOM-read invariant](../contracts/behavioral-contracts.md#5-dom-read-invariant).

---

## Related Documents

- [`../api-reference/html-structure.md`](../api-reference/html-structure.md) — the full
  report-card element/ID reference.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the
  **rebuild-from-scratch** invariant (single source of truth) and all other behavioral contracts.
- [`computation.md`](computation.md) — F-003 / F-004, the source of the rendered `total` and
  `percentage`.
- [`grading.md`](grading.md) — F-005, the source of the rendered `grade`.
- [`pdf-export.md`](pdf-export.md) — F-007, the downstream consumer that reads this rendered DOM.
- [`../index.md`](../index.md) — back to the Documentation Hub.
