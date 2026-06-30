# HTML Structure Reference

## Purpose

This page is the **DOM element / ID reference** for the Student Report Generator's markup — the exact set of element IDs, their types and roles, the inline `onclick` function wiring, and the **structural contract** that the application's logic depends on. It documents the markup that the two global functions read from and write to, and it is the precise companion to the data-entry feature guide [`../functionality/data-entry.md`](../functionality/data-entry.md) and the report-rendering feature guide [`../functionality/report-rendering.md`](../functionality/report-rendering.md).

All content on this page is **code-grounded**: every technical claim carries an inline `[Readme.md:Lx-Ly]` citation pointing at the `index.html` markup embedded in the repository-root `Readme.md`. The standalone `index.html` implied by the README's project tree does not physically exist, so `Readme.md` is the sole source. This is a documentation-only reference and does not modify any source code.

## Source Location

**Source:** [Readme.md:L42-L100]

The application markup is embedded as an `html` fenced block inside `Readme.md`. The `<head>` resource tags occupy [Readme.md:L45-L54]; the `<body>` content — the input form, the action buttons, and the report-card region — is wrapped in `<div class="container">` and spans [Readme.md:L57-L94]; the application script is included at the end of `<body>` at [Readme.md:L96].

---

## DOM Element / ID Reference

The markup contains **three groups** of meaningful elements: the **form inputs** the user fills in, the two **action buttons**, and the **report-card placeholders** that receive the rendered output. The tables below list each element, its `id` (where present), its type / role, and which function reads or writes it. The read / write attributions are cross-referenced against the embedded `script.js` [Readme.md:L176-L253]; for the full per-function contracts see [`script-js.md`](script-js.md).

### Form Inputs

The form inputs live inside the `<div class="form-section">` [Readme.md:L60]. The two text fields are declared at [Readme.md:L61-L62] and the five `type="number"` subject fields at [Readme.md:L64-L68]. All seven are read by `generateReport()`; none is read by `downloadPDF()` (see the DOM-read invariant below).

| Element | id | Type / Role | Read or Written by |
| --- | --- | --- | --- |
| `<input>` | `studentName` | text — student name | read by `generateReport()` [Readme.md:L178] |
| `<input>` | `rollNumber` | text — roll number | read by `generateReport()` [Readme.md:L179] |
| `<input>` | `maths` | number — Maths marks | read by `generateReport()` [Readme.md:L182] |
| `<input>` | `science` | number — Science marks | read by `generateReport()` [Readme.md:L183] |
| `<input>` | `english` | number — English marks | read by `generateReport()` [Readme.md:L184] |
| `<input>` | `history` | number — History marks | read by `generateReport()` [Readme.md:L185] |
| `<input>` | `computer` | number — Computer marks | read by `generateReport()` [Readme.md:L186] |

### Action Buttons

The two buttons are declared at [Readme.md:L70-L71]. They have **no `id`** — they are wired purely by inline `onclick` attributes (see [Function Wiring](#function-wiring) below).

| Element | id | Type / Role | Read or Written by |
| --- | --- | --- | --- |
| `<button>` | *(none)* | "Generate Report" — triggers compute + render | `onclick="generateReport()"` [Readme.md:L70] |
| `<button>` | *(none)* | "Download PDF" — triggers PDF export | `onclick="downloadPDF()"` [Readme.md:L71] |

### Report-Card Placeholders

The report-card region is the `<div id="reportCard" class="report-card">` container [Readme.md:L74-L93]. It is **always present** in the DOM — the code never hides or shows it — and its placeholder `<span>` elements simply hold empty text until `generateReport()` runs [Readme.md:L74-L93].

| Element | id | Type / Role | Read or Written by |
| --- | --- | --- | --- |
| `<div>` | `reportCard` | container (`class="report-card"`) for the rendered report | static container — not accessed via `getElementById` [Readme.md:L74] |
| `<span>` | `rName` | rendered student name | written by `generateReport()` [Readme.md:L223]; read by `downloadPDF()` [Readme.md:L235] |
| `<span>` | `rRoll` | rendered roll number | written by `generateReport()` [Readme.md:L224]; read by `downloadPDF()` [Readme.md:L236] |
| `<tbody>` | `marksTable` | per-subject marks rows | cleared + appended by `generateReport()` [Readme.md:L191-L192] [Readme.md:L204] |
| `<span>` | `totalMarks` | rendered total | written by `generateReport()` [Readme.md:L225]; read by `downloadPDF()` [Readme.md:L237] |
| `<span>` | `percentage` | rendered percentage (2 decimals) | written by `generateReport()` [Readme.md:L226]; read by `downloadPDF()` [Readme.md:L238] |
| `<span>` | `grade` | rendered grade | written by `generateReport()` [Readme.md:L227]; read by `downloadPDF()` [Readme.md:L239] |

Two structural notes about this region:

- **`#marksTable` is a `<tbody>`, not a `<table>`** [Readme.md:L87]. The surrounding `<table>` and its `<thead>` header row (columns **Subject** and **Marks**) are **static** markup [Readme.md:L80-L86]; only the `<tbody>` rows are dynamically rebuilt on each run.
- **The `%` after the percentage is literal markup text** [Readme.md:L91], not produced by JavaScript. `generateReport()` writes only the numeric `.toFixed(2)` string into `#percentage` [Readme.md:L226]; the trailing `%` glyph is fixed in the HTML.

### Head Resources (non-input)

For completeness, the `<head>` and end-of-body tags reference three external resources plus the application script. These are **not** form inputs or report-card placeholders:

| Tag | Purpose | Source |
| --- | --- | --- |
| `<meta name="viewport">` | responsive viewport — the only responsiveness mechanism (no `@media` rules) | [Readme.md:L47] |
| `<link rel="stylesheet">` | loads `style.css` | [Readme.md:L50] |
| `<script src="…jspdf…">` | loads jsPDF 2.5.1 from the cdnjs CDN | [Readme.md:L53] |
| `<script src="script.js">` | loads the application logic at the end of `<body>` | [Readme.md:L96] |

For the styling details see [`css-reference.md`](css-reference.md); for the jsPDF CDN integration and its availability precondition see [`../dependencies.md`](../dependencies.md).

---

## Function Wiring

The application registers **no event listeners in JavaScript**; instead, the two `<button>` elements are wired to global functions through inline `onclick` attributes [Readme.md:L70-L71]:

```html
<button onclick="generateReport()">Generate Report</button>
<button onclick="downloadPDF()">Download PDF</button>
```

The application logic is loaded at the **end of `<body>`** via `<script src="script.js"></script>` [Readme.md:L96], so by the time the user can click either button, both `generateReport()` and `downloadPDF()` are already defined as globals.

**How IDs connect markup to logic.** The logic in `script.js` reaches the markup exclusively through `document.getElementById('<id>')`: it reads the form inputs by their IDs and writes — then later re-reads — the report-card placeholders by their IDs [Readme.md:L176-L253]. The element IDs in the tables above are therefore the contract surface between the HTML and the JavaScript. For the exact function signatures and the complete per-function list of reads and writes, see [`script-js.md`](script-js.md); those contracts are **not** duplicated here.

**The DOM-read invariant.** `downloadPDF()` reads the **rendered report-card placeholders** (`#rName`, `#rRoll`, `#totalMarks`, `#percentage`, `#grade`), **not** the form inputs [Readme.md:L235-L239]. As a direct consequence, `generateReport()` must run before `downloadPDF()`. The full invariant is defined in the single source of truth, [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).

---

## Expected Behavior / Contract

This section is the page's direct answer to *"highlight what is the expectation from the code."* It states the **structural contract** that the markup must satisfy for `script.js` to function.

**Required IDs (precondition).** The application logic assumes **13 element IDs** exist in the DOM, spelled exactly as below — the **7 form inputs** plus the **6 report-card placeholders** [Readme.md:L61-L68] [Readme.md:L74-L93]:

| Group | Required IDs | Accessed by |
| --- | --- | --- |
| Form inputs (7) | `#studentName`, `#rollNumber`, `#maths`, `#science`, `#english`, `#history`, `#computer` | read by `generateReport()` [Readme.md:L178-L186] |
| Report-card placeholders (6) | `#rName`, `#rRoll`, `#marksTable`, `#totalMarks`, `#percentage`, `#grade` | written / rebuilt by `generateReport()` [Readme.md:L191-L192], [Readme.md:L223-L227]; five re-read by `downloadPDF()` [Readme.md:L235-L239] |

The static `#reportCard` container [Readme.md:L74] is always present in the DOM but is **not** accessed via `getElementById`, so it is not counted among the 13 required IDs.

**Failure mode (documented expectation, not a fix).** There is **no defensive null-checking** anywhere in the code [Readme.md:L176-L253]. If an expected ID is missing or renamed, `document.getElementById('<id>')` returns `null`, and the subsequent `.value` read (in `generateReport()`) or `.innerText` read / write throws a `TypeError`, so report generation fails. This is the expected behavior of the markup-to-logic contract, stated here as an expectation rather than a recommended change.

**Case sensitivity.** Element IDs are **case-sensitive** and must match the `getElementById` argument strings exactly — for example, `#studentName` is not interchangeable with `#studentname` [Readme.md:L61], [Readme.md:L178].

**Wiring contract.** The global functions `generateReport()` and `downloadPDF()` must be defined for the inline `onclick` handlers [Readme.md:L70-L71] to work; they are, because `script.js` is included at the end of `<body>` [Readme.md:L96].

---

## Related Documents

This page is part of a hub-and-spoke documentation set. Related references (paths relative to `docs/api-reference/`):

- [`script-js.md`](script-js.md) — the `generateReport()` / `downloadPDF()` function reference: exact signatures and the complete per-function DOM reads and writes.
- [`css-reference.md`](css-reference.md) — the selector / style reference for the markup documented here.
- [`../functionality/data-entry.md`](../functionality/data-entry.md) — the data-entry feature (**F-001**, **F-002**) that uses the form inputs.
- [`../functionality/report-rendering.md`](../functionality/report-rendering.md) — the on-screen rendering feature (**F-006**) that writes these placeholders.
- [`../reference/data-schema.md`](../reference/data-schema.md) — the single source of truth for the fixed subject input IDs (`maths`, `science`, `english`, `history`, `computer`) and the score schema (per-subject maximum, 500-point total, grade thresholds) referenced by the form-input IDs above.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the single source of truth for invariants, including the DOM-read invariant and rebuild-from-scratch rendering.
- [`../index.md`](../index.md) — back to the Documentation Hub.

---

*Maintenance note: this reference is derived from the application markup embedded in `Readme.md` [Readme.md:L42-L100]. If that embedded `index.html` block changes — element IDs added, renamed, or removed — update the tables, the required-IDs contract, and the cited line ranges here so this reference stays accurate.*
