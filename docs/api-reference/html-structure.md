# HTML Structure Reference

## Purpose

This page is the **DOM element / ID reference** for the Student Report Generator's markup. It
catalogs every element `id` the application relies on, the role of each element, the inline
`onclick` **function wiring**, and the **structural contract** that the behavioral logic in
`script.js` depends on. It is the precise companion to the data-entry feature guide
[`../functionality/data-entry.md`](../functionality/data-entry.md) (feature **F-001 / F-002**,
which uses the **form inputs**) and the report-rendering feature guide
[`../functionality/report-rendering.md`](../functionality/report-rendering.md) (feature
**F-006**, which writes the **report-card placeholders**).

All content is **code-grounded**: every technical claim carries an inline `[Readme.md:Lx-Ly]`
citation back to the application source, which is embedded in the repository-root `Readme.md`.
Code excerpts are short, verbatim quotations of the cited lines. The standalone `index.html`
implied by the README's project tree does not physically exist as a separate file
[Readme.md:L14-L21]; `Readme.md` is the sole source.

> **Single source of truth (SSOT).** This page documents the *markup structure and element IDs*.
> It does **not** restate the full function contracts (see [`script-js.md`](script-js.md)) or the
> complete invariant catalog (see
> [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md)); it links to
> them instead.

## Source Location

`Source: [Readme.md:L27-L85]` — the embedded `index.html` fenced code block.

---

## DOM Element / ID Reference

The markup defines **thirteen** identified elements that `script.js` addresses by `id`: seven
**form inputs** that `generateReport()` reads, and six **report-card placeholders** that
`generateReport()` writes and `downloadPDF()` re-reads. The two action buttons carry **no `id`** —
they are wired purely by inline `onclick` attributes [Readme.md:L55-L56]. The sub-tables below
group these elements by role; the **Read or Written by** column cross-references the exact
`script.js` line that touches each element.

### Form Inputs

The seven `<input>` fields live inside the `<div class="form-section">` block [Readme.md:L45]. Two
are `type="text"` identity fields [Readme.md:L46-L47]; five are `type="number"` subject-mark
fields [Readme.md:L49-L53].

| Element | id | Type / Role | Read or Written by |
|---|---|---|---|
| `<input>` | `studentName` | text — student name | read by `generateReport()` [Readme.md:L163] |
| `<input>` | `rollNumber` | text — roll number | read by `generateReport()` [Readme.md:L164] |
| `<input>` | `maths` | number — Maths marks | read by `generateReport()` [Readme.md:L167] |
| `<input>` | `science` | number — Science marks | read by `generateReport()` [Readme.md:L168] |
| `<input>` | `english` | number — English marks | read by `generateReport()` [Readme.md:L169] |
| `<input>` | `history` | number — History marks | read by `generateReport()` [Readme.md:L170] |
| `<input>` | `computer` | number — Computer marks | read by `generateReport()` [Readme.md:L171] |

### Action Buttons

The two `<button>` elements have **no `id`**; each is wired to a global function via an inline
`onclick` attribute [Readme.md:L55-L56].

| Element | id | Type / Role | Read or Written by |
|---|---|---|---|
| `<button>` | *(none)* | "Generate Report" — triggers compute + render | `onclick="generateReport()"` [Readme.md:L55] |
| `<button>` | *(none)* | "Download PDF" — triggers PDF export | `onclick="downloadPDF()"` [Readme.md:L56] |

### Report-Card Placeholders

The report-card region is a static `<div id="reportCard" class="report-card">` [Readme.md:L59]
that is **always present** in the rendered DOM — the code never hides or shows it; its placeholder
`<span>` elements simply stay empty until `generateReport()` runs [Readme.md:L59-L78]. Note that
`marksTable` is the `<tbody>` [Readme.md:L72], **not** the surrounding `<table>`: the
`<table>`/`<thead>` (columns **Subject** / **Marks**) is static [Readme.md:L65-L71], and only the
`<tbody>` rows are rebuilt on each run.

| Element | id | Type / Role | Read or Written by |
|---|---|---|---|
| `<div>` | `reportCard` | container (`class="report-card"`) for the rendered report | static container [Readme.md:L59] |
| `<span>` | `rName` | rendered student name | written by `generateReport()` [Readme.md:L208]; read by `downloadPDF()` [Readme.md:L220] |
| `<span>` | `rRoll` | rendered roll number | written by `generateReport()` [Readme.md:L209]; read by `downloadPDF()` [Readme.md:L221] |
| `<tbody>` | `marksTable` | per-subject marks rows (one `<tr>` per subject) | cleared + appended by `generateReport()` [Readme.md:L176-L177], [Readme.md:L189] |
| `<span>` | `totalMarks` | rendered total marks | written by `generateReport()` [Readme.md:L210]; read by `downloadPDF()` [Readme.md:L222] |
| `<span>` | `percentage` | rendered percentage, 2 decimals (a literal `%` follows in the markup) | written by `generateReport()` [Readme.md:L211]; read by `downloadPDF()` [Readme.md:L223] |
| `<span>` | `grade` | rendered grade letter | written by `generateReport()` [Readme.md:L212]; read by `downloadPDF()` [Readme.md:L224] |

> **Note — the `%` is literal markup.** The percent sign shown after the percentage is **literal
> text in the markup** [Readme.md:L76], not produced by JavaScript; `generateReport()` writes only
> the numeric `.toFixed(2)` string into `#percentage` [Readme.md:L211].

### `<head>` Resource Tags (not addressed by `id`)

The document `<head>` loads three resources referenced by attribute rather than by `id`, plus the
script include at the end of `<body>`. These are **not** inputs and are listed here only for
completeness.

| Tag | Purpose | Source |
|---|---|---|
| `<meta name="viewport">` | responsive scaling — the only responsiveness mechanism (no `@media` rules; see [`css-reference.md`](css-reference.md)) | [Readme.md:L32] |
| `<link rel="stylesheet">` | loads `style.css` | [Readme.md:L35] |
| `<script src="…jspdf…">` | loads jsPDF 2.5.1 from cdnjs, populating the `window.jspdf` global | [Readme.md:L38] |
| `<script src="script.js">` | loads the behavioral logic at the end of `<body>` | [Readme.md:L81] |

---

## Function Wiring

The application connects markup to logic in two ways: **inline `onclick` attributes** on the
buttons, and **`document.getElementById('<id>')`** lookups inside `script.js`.

The two action buttons are wired to the global functions `generateReport()` [Readme.md:L55] and
`downloadPDF()` [Readme.md:L56] through inline `onclick` attributes [Readme.md:L55-L56]:

```html
<button onclick="generateReport()">Generate Report</button>
<button onclick="downloadPDF()">Download PDF</button>
```

Because `script.js` is loaded by a `<script>` tag at the very end of `<body>` [Readme.md:L81],
both functions are defined as **globals** by the time the rendered page lets the user click either
button.

Each identified element is connected to the logic by `id`: `script.js` calls
`document.getElementById('<id>')` to read the **form inputs** and to write — then later re-read —
the **report-card placeholders**. For the exact function signatures and the full per-function
lists of reads, writes, side effects, and error behavior, see the function reference
[`script-js.md`](script-js.md); this page does not duplicate those contracts.

**DOM-read invariant.** `downloadPDF()` reads the **rendered** report-card placeholders
(`#rName`, `#rRoll`, `#totalMarks`, `#percentage`, `#grade`), **not** the form inputs
[Readme.md:L220-L224] — which is why **Generate Report** must run before **Download PDF**. The
invariant is defined in full in
[`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).

---

## Expected Behavior / Contract

This section states the **structural contract** the markup must satisfy for `script.js` to
function — the page's direct answer to *"what is the expectation from the code?"*.

### Required IDs (precondition)

Every `id` below **must exist in the DOM, spelled exactly as shown**, for the corresponding
`script.js` access to succeed. The seven form-input IDs are read by `generateReport()`
[Readme.md:L46-L53]; the six report-card IDs are written by `generateReport()` and re-read by
`downloadPDF()` [Readme.md:L59-L78].

| Required id | Group | Accessed by |
|---|---|---|
| `studentName` | form input | `generateReport()` read [Readme.md:L163] |
| `rollNumber` | form input | `generateReport()` read [Readme.md:L164] |
| `maths` | form input | `generateReport()` read [Readme.md:L167] |
| `science` | form input | `generateReport()` read [Readme.md:L168] |
| `english` | form input | `generateReport()` read [Readme.md:L169] |
| `history` | form input | `generateReport()` read [Readme.md:L170] |
| `computer` | form input | `generateReport()` read [Readme.md:L171] |
| `marksTable` | report card | `generateReport()` write [Readme.md:L176-L177], [Readme.md:L189] |
| `rName` | report card | `generateReport()` write [Readme.md:L208] / `downloadPDF()` read [Readme.md:L220] |
| `rRoll` | report card | `generateReport()` write [Readme.md:L209] / `downloadPDF()` read [Readme.md:L221] |
| `totalMarks` | report card | `generateReport()` write [Readme.md:L210] / `downloadPDF()` read [Readme.md:L222] |
| `percentage` | report card | `generateReport()` write [Readme.md:L211] / `downloadPDF()` read [Readme.md:L223] |
| `grade` | report card | `generateReport()` write [Readme.md:L212] / `downloadPDF()` read [Readme.md:L224] |

### Failure mode

There is **no defensive null-checking** anywhere in the code [Readme.md:L161-L238]. If any required
`id` is missing or renamed, `document.getElementById('<id>')` returns `null`, and the subsequent
`.value` read (in `generateReport()`) or `.innerText` read/write throws a `TypeError` — report
generation or PDF export then fails. This is documented here as the **expected behavior** of the
current code, not as a defect to be patched in this documentation task.

### Case sensitivity

Element IDs are **case-sensitive** and must match the `getElementById` arguments exactly:
`studentName` is not interchangeable with `studentname` or `StudentName`. A casing mismatch is
treated as a missing element and triggers the failure mode above.

### Wiring contract

The global functions `generateReport()` and `downloadPDF()` must be **defined** for the buttons'
inline `onclick` handlers [Readme.md:L55-L56] to work. They are defined by `script.js`, which is
included at the end of `<body>` [Readme.md:L81], so the wiring holds for normal page loads.

---

## Related Documents

- [`script-js.md`](script-js.md) — the `generateReport()` / `downloadPDF()` function reference
  (exact signatures, reads, writes, and errors).
- [`css-reference.md`](css-reference.md) — the selector / style reference for this markup.
- [`../functionality/data-entry.md`](../functionality/data-entry.md) — the data-entry feature
  (**F-001 / F-002**) that uses the form inputs.
- [`../functionality/report-rendering.md`](../functionality/report-rendering.md) — the on-screen
  rendering feature (**F-006**) that writes the report-card placeholders.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — invariants
  (DOM-read invariant, rebuild-from-scratch) and preconditions.
- [`../index.md`](../index.md) — back to the Documentation Hub.
