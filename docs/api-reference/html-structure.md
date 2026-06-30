# HTML Structure Reference

## Purpose

This page is the **DOM element / ID reference** for the Student Report Generator's markup — the exact set of element IDs, their types and roles, the inline `onclick` function wiring, and the **structural contract** that the application's logic depends on. It documents the markup that the two global functions read from and write to, and it is the precise companion to the data-entry feature guide [`../functionality/data-entry.md`](../functionality/data-entry.md) and the report-rendering feature guide [`../functionality/report-rendering.md`](../functionality/report-rendering.md).

All content on this page is **code-grounded**: every technical claim carries an inline `[Readme.md:Lx-Ly]` citation pointing at the `index.html` markup embedded in the repository-root `Readme.md`. The standalone `index.html` implied by the README's project tree does not physically exist, so `Readme.md` is the sole source. This is a documentation-only reference and does not modify any source code.

## Source Location

**Source:** [Readme.md:L27-L85]

The application markup is embedded as an `html` fenced block inside `Readme.md`. The `<head>` resource tags occupy [Readme.md:L30-L39]; the `<body>` content — the input form, the action buttons, and the report-card region — is wrapped in `<div class="container">` and spans [Readme.md:L42-L79]; the application script is included at the end of `<body>` at [Readme.md:L81].

---

## DOM Element / ID Reference

The markup contains **three groups** of meaningful elements: the **form inputs** the user fills in, the two **action buttons**, and the **report-card placeholders** that receive the rendered output. The tables below list each element, its `id` (where present), its type / role, and which function reads or writes it. The read / write attributions are cross-referenced against the embedded `script.js` [Readme.md:L161-L238]; for the full per-function contracts see [`script-js.md`](script-js.md).

### Form Inputs

The form inputs live inside the `<div class="form-section">` [Readme.md:L45]. The two text fields are declared at [Readme.md:L46-L47] and the five `type="number"` subject fields at [Readme.md:L49-L53]. All seven are read by `generateReport()`; none is read by `downloadPDF()` (see the DOM-read invariant below).

| Element | id | Type / Role | Read or Written by |
| --- | --- | --- | --- |
| `<input>` | `studentName` | text — student name | read by `generateReport()` [Readme.md:L163] |
| `<input>` | `rollNumber` | text — roll number | read by `generateReport()` [Readme.md:L164] |
| `<input>` | `maths` | number — Maths marks | read by `generateReport()` [Readme.md:L167] |
| `<input>` | `science` | number — Science marks | read by `generateReport()` [Readme.md:L168] |
| `<input>` | `english` | number — English marks | read by `generateReport()` [Readme.md:L169] |
| `<input>` | `history` | number — History marks | read by `generateReport()` [Readme.md:L170] |
| `<input>` | `computer` | number — Computer marks | read by `generateReport()` [Readme.md:L171] |

### Action Buttons

The two buttons are declared at [Readme.md:L55-L56]. They have **no `id`** — they are wired purely by inline `onclick` attributes (see [Function Wiring](#function-wiring) below).

| Element | id | Type / Role | Read or Written by |
| --- | --- | --- | --- |
| `<button>` | *(none)* | "Generate Report" — triggers compute + render | `onclick="generateReport()"` [Readme.md:L55] |
| `<button>` | *(none)* | "Download PDF" — triggers PDF export | `onclick="downloadPDF()"` [Readme.md:L56] |

### Report-Card Placeholders

The report-card region is the `<div id="reportCard" class="report-card">` container [Readme.md:L59-L78]. It is **always present** in the DOM — the code never hides or shows it — and its placeholder `<span>` elements simply hold empty text until `generateReport()` runs [Readme.md:L59-L78].

| Element | id | Type / Role | Read or Written by |
| --- | --- | --- | --- |
| `<div>` | `reportCard` | container (`class="report-card"`) for the rendered report | static container — not accessed via `getElementById` [Readme.md:L59] |
| `<span>` | `rName` | rendered student name | written by `generateReport()` [Readme.md:L208]; read by `downloadPDF()` [Readme.md:L220] |
| `<span>` | `rRoll` | rendered roll number | written by `generateReport()` [Readme.md:L209]; read by `downloadPDF()` [Readme.md:L221] |
| `<tbody>` | `marksTable` | per-subject marks rows | cleared + appended by `generateReport()` [Readme.md:L176-L177, L189] |
| `<span>` | `totalMarks` | rendered total | written by `generateReport()` [Readme.md:L210]; read by `downloadPDF()` [Readme.md:L222] |
| `<span>` | `percentage` | rendered percentage (2 decimals) | written by `generateReport()` [Readme.md:L211]; read by `downloadPDF()` [Readme.md:L223] |
| `<span>` | `grade` | rendered grade | written by `generateReport()` [Readme.md:L212]; read by `downloadPDF()` [Readme.md:L224] |

Two structural notes about this region:

- **`#marksTable` is a `<tbody>`, not a `<table>`** [Readme.md:L72]. The surrounding `<table>` and its `<thead>` header row (columns **Subject** and **Marks**) are **static** markup [Readme.md:L65-L71]; only the `<tbody>` rows are dynamically rebuilt on each run.
- **The `%` after the percentage is literal markup text** [Readme.md:L76], not produced by JavaScript. `generateReport()` writes only the numeric `.toFixed(2)` string into `#percentage` [Readme.md:L211]; the trailing `%` glyph is fixed in the HTML.

### Head Resources (non-input)

For completeness, the `<head>` and end-of-body tags reference three external resources plus the application script. These are **not** form inputs or report-card placeholders:

| Tag | Purpose | Source |
| --- | --- | --- |
| `<meta name="viewport">` | responsive viewport — the only responsiveness mechanism (no `@media` rules) | [Readme.md:L32] |
| `<link rel="stylesheet">` | loads `style.css` | [Readme.md:L35] |
| `<script src="…jspdf…">` | loads jsPDF 2.5.1 from the cdnjs CDN | [Readme.md:L38] |
| `<script src="script.js">` | loads the application logic at the end of `<body>` | [Readme.md:L81] |

For the styling details see [`css-reference.md`](css-reference.md); for the jsPDF CDN integration and its availability precondition see [`../dependencies.md`](../dependencies.md).

---

## Function Wiring

The application registers **no event listeners in JavaScript**; instead, the two `<button>` elements are wired to global functions through inline `onclick` attributes [Readme.md:L55-L56]:

```html
<button onclick="generateReport()">Generate Report</button>
<button onclick="downloadPDF()">Download PDF</button>
```

The application logic is loaded at the **end of `<body>`** via `<script src="script.js"></script>` [Readme.md:L81], so by the time the user can click either button, both `generateReport()` and `downloadPDF()` are already defined as globals.

**How IDs connect markup to logic.** The logic in `script.js` reaches the markup exclusively through `document.getElementById('<id>')`: it reads the form inputs by their IDs and writes — then later re-reads — the report-card placeholders by their IDs [Readme.md:L161-L238]. The element IDs in the tables above are therefore the contract surface between the HTML and the JavaScript. For the exact function signatures and the complete per-function list of reads and writes, see [`script-js.md`](script-js.md); those contracts are **not** duplicated here.

**The DOM-read invariant.** `downloadPDF()` reads the **rendered report-card placeholders** (`#rName`, `#rRoll`, `#totalMarks`, `#percentage`, `#grade`), **not** the form inputs [Readme.md:L220-L224]. As a direct consequence, `generateReport()` must run before `downloadPDF()`. The full invariant is defined in the single source of truth, [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).

---

## Expected Behavior / Contract

This section is the page's direct answer to *"highlight what is the expectation from the code."* It states the **structural contract** that the markup must satisfy for `script.js` to function.

**Required IDs (precondition).** The application logic assumes **13 element IDs** exist in the DOM, spelled exactly as below — the **7 form inputs** plus the **6 report-card placeholders** [Readme.md:L46-L53, L59-L78]:

| Group | Required IDs | Accessed by |
| --- | --- | --- |
| Form inputs (7) | `#studentName`, `#rollNumber`, `#maths`, `#science`, `#english`, `#history`, `#computer` | read by `generateReport()` [Readme.md:L163-L171] |
| Report-card placeholders (6) | `#rName`, `#rRoll`, `#marksTable`, `#totalMarks`, `#percentage`, `#grade` | written / rebuilt by `generateReport()` [Readme.md:L176-L177], [Readme.md:L208-L212]; five re-read by `downloadPDF()` [Readme.md:L220-L224] |

The static `#reportCard` container [Readme.md:L59] is always present in the DOM but is **not** accessed via `getElementById`, so it is not counted among the 13 required IDs.

**Failure mode (documented expectation, not a fix).** There is **no defensive null-checking** anywhere in the code [Readme.md:L161-L238]. If an expected ID is missing or renamed, `document.getElementById('<id>')` returns `null`, and the subsequent `.value` read (in `generateReport()`) or `.innerText` read / write throws a `TypeError`, so report generation fails. This is the expected behavior of the markup-to-logic contract, stated here as an expectation rather than a recommended change.

**Case sensitivity.** Element IDs are **case-sensitive** and must match the `getElementById` argument strings exactly — for example, `#studentName` is not interchangeable with `#studentname` [Readme.md:L46], [Readme.md:L163].

**Wiring contract.** The global functions `generateReport()` and `downloadPDF()` must be defined for the inline `onclick` handlers [Readme.md:L55-L56] to work; they are, because `script.js` is included at the end of `<body>` [Readme.md:L81].

---

## Related Documents

This page is part of a hub-and-spoke documentation set. Related references (paths relative to `docs/api-reference/`):

- [`script-js.md`](script-js.md) — the `generateReport()` / `downloadPDF()` function reference: exact signatures and the complete per-function DOM reads and writes.
- [`css-reference.md`](css-reference.md) — the selector / style reference for the markup documented here.
- [`../functionality/data-entry.md`](../functionality/data-entry.md) — the data-entry feature (**F-001**, **F-002**) that uses the form inputs.
- [`../functionality/report-rendering.md`](../functionality/report-rendering.md) — the on-screen rendering feature (**F-006**) that writes these placeholders.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the single source of truth for invariants, including the DOM-read invariant and rebuild-from-scratch rendering.
- [`../index.md`](../index.md) — back to the Documentation Hub.

---

*Maintenance note: this reference is derived from the application markup embedded in `Readme.md` [Readme.md:L27-L85]. If that embedded `index.html` block changes — element IDs added, renamed, or removed — update the tables, the required-IDs contract, and the cited line ranges here so this reference stays accurate.*
