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

All content is **code-grounded**: every technical claim carries an inline `Readme.md` line-range
citation back to the application source, which is embedded in the repository-root `Readme.md`.
Code excerpts are short, verbatim quotations of the cited lines. The standalone `index.html`
implied by the README's project tree does not physically exist as a separate file
[Readme.md:L29-L36]; `Readme.md` is the sole source.

> **Single source of truth (SSOT).** This page documents the *markup structure and element IDs*.
> It does **not** restate the full function contracts (see [`script-js.md`](script-js.md)) or the
> complete invariant catalog (see
> [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md)); it links to
> them instead.

## Source Location

`Source: [Readme.md:L42-L100]` — the embedded `index.html` fenced code block.

---

## How It Works

The markup defines the elements that the behavioral logic in `script.js` addresses **by `id`**, plus two action buttons wired by inline `onclick` attributes [Readme.md:L70-L71]. There is no framework and no event-listener registration — the wiring is entirely declarative HTML, and `script.js` loads last, at the end of `<body>`, so the DOM exists before it runs [Readme.md:L96].

- **Form inputs (read).** Seven `<input>` fields — two identity fields and five subject-mark fields [Readme.md:L61-L68] — are read by `generateReport()` via `document.getElementById(...)` when **Generate Report** is clicked [Readme.md:L178-L187].
- **Report-card placeholders (written, then re-read).** Six placeholder elements inside the always-present `#reportCard` block [Readme.md:L74-L93] are populated by `generateReport()`; **five** of them — `#rName`, `#rRoll`, `#totalMarks`, `#percentage`, `#grade` [Readme.md:L223-L227] — are later **re-read** by `downloadPDF()` (the **DOM-read invariant** [Readme.md:L235-L239]), while `#marksTable` is rebuilt [Readme.md:L191-L204] but **not** re-read.
- **Buttons (wiring only).** The two action buttons carry **no `id`**; they invoke `generateReport()` and `downloadPDF()` purely through inline `onclick` [Readme.md:L70-L71].

The structural **contract** is therefore that every `id` below must exist in the DOM, spelled exactly as shown, for the script to function. The detailed element/ID reference and the function wiring follow.

---

## DOM Element / ID Reference

The markup defines **thirteen** identified elements that `script.js` addresses by `id`: seven
**form inputs** that `generateReport()` reads, and six **report-card placeholders** that
`generateReport()` writes — **five** of which `downloadPDF()` then re-reads, while `#marksTable` is
rebuilt but not re-read. The two action buttons carry **no `id`** —
they are wired purely by inline `onclick` attributes [Readme.md:L70-L71]. The sub-tables below
group these elements by role; the **Read or Written by** column cross-references the exact
`script.js` line that touches each element.

### Form Inputs

The seven `<input>` fields live inside the `<div class="form-section">` block [Readme.md:L60]. Two
are `type="text"` identity fields [Readme.md:L61-L62]; five are `type="number"` subject-mark
fields [Readme.md:L64-L68].

| Element | id | Type / Role | Read or Written by |
|---|---|---|---|
| `<input>` | `studentName` | text — student name | read by `generateReport()` [Readme.md:L178] |
| `<input>` | `rollNumber` | text — roll number | read by `generateReport()` [Readme.md:L179] |
| `<input>` | `maths` | number — Maths marks | read by `generateReport()` [Readme.md:L182] |
| `<input>` | `science` | number — Science marks | read by `generateReport()` [Readme.md:L183] |
| `<input>` | `english` | number — English marks | read by `generateReport()` [Readme.md:L184] |
| `<input>` | `history` | number — History marks | read by `generateReport()` [Readme.md:L185] |
| `<input>` | `computer` | number — Computer marks | read by `generateReport()` [Readme.md:L186] |

### Action Buttons

The two `<button>` elements have **no `id`**; each is wired to a global function via an inline
`onclick` attribute [Readme.md:L70-L71].

| Element | id | Type / Role | Read or Written by |
|---|---|---|---|
| `<button>` | *(none)* | "Generate Report" — triggers compute + render | `onclick="generateReport()"` [Readme.md:L70] |
| `<button>` | *(none)* | "Download PDF" — triggers PDF export | `onclick="downloadPDF()"` [Readme.md:L71] |

### Report-Card Placeholders

The report-card region is a static `<div id="reportCard" class="report-card">` [Readme.md:L74]
that is **always present** in the rendered DOM — the code never hides or shows it; its placeholder
`<span>` elements simply stay empty until `generateReport()` runs [Readme.md:L74-L93]. Note that
`marksTable` is the `<tbody>` [Readme.md:L87], **not** the surrounding `<table>`: the
`<table>`/`<thead>` (columns **Subject** / **Marks**) is static [Readme.md:L80-L86], and only the
`<tbody>` rows are rebuilt on each run.

| Element | id | Type / Role | Read or Written by |
|---|---|---|---|
| `<div>` | `reportCard` | container (`class="report-card"`) for the rendered report | static container [Readme.md:L74] |
| `<span>` | `rName` | rendered student name | written by `generateReport()` [Readme.md:L223]; read by `downloadPDF()` [Readme.md:L235] |
| `<span>` | `rRoll` | rendered roll number | written by `generateReport()` [Readme.md:L224]; read by `downloadPDF()` [Readme.md:L236] |
| `<tbody>` | `marksTable` | per-subject marks rows (one `<tr>` per subject) | cleared + appended by `generateReport()` [Readme.md:L191-L192], [Readme.md:L204] |
| `<span>` | `totalMarks` | rendered total marks | written by `generateReport()` [Readme.md:L225]; read by `downloadPDF()` [Readme.md:L237] |
| `<span>` | `percentage` | rendered percentage, 2 decimals (a literal `%` follows in the markup) | written by `generateReport()` [Readme.md:L226]; read by `downloadPDF()` [Readme.md:L238] |
| `<span>` | `grade` | rendered grade letter | written by `generateReport()` [Readme.md:L227]; read by `downloadPDF()` [Readme.md:L239] |

> **Note — the `%` is literal markup.** The percent sign shown after the percentage is **literal
> text in the markup** [Readme.md:L91], not produced by JavaScript; `generateReport()` writes only
> the numeric `.toFixed(2)` string into `#percentage` [Readme.md:L226].

### `<head>` Resource Tags (not addressed by `id`)

The document `<head>` loads three resources referenced by attribute rather than by `id`, plus the
script include at the end of `<body>`. These are **not** inputs and are listed here only for
completeness.

| Tag | Purpose | Source |
|---|---|---|
| `<meta name="viewport">` | responsive scaling — the only responsiveness mechanism (no `@media` rules; see [`css-reference.md`](css-reference.md)) | [Readme.md:L47] |
| `<link rel="stylesheet">` | loads `style.css` | [Readme.md:L50] |
| `<script src="…jspdf…">` | loads jsPDF 2.5.1 from cdnjs, populating the `window.jspdf` global | [Readme.md:L53] |
| `<script src="script.js">` | loads the behavioral logic at the end of `<body>` | [Readme.md:L96] |

---

## Function Wiring

The application connects markup to logic in two ways: **inline `onclick` attributes** on the
buttons, and **`document.getElementById('<id>')`** lookups inside `script.js`.

The two action buttons are wired to the global functions `generateReport()` [Readme.md:L70] and
`downloadPDF()` [Readme.md:L71] through inline `onclick` attributes [Readme.md:L70-L71]:

```html
<button onclick="generateReport()">Generate Report</button>
<button onclick="downloadPDF()">Download PDF</button>
```

Because `script.js` is loaded by a `<script>` tag at the very end of `<body>` [Readme.md:L96],
both functions are defined as **globals** by the time the rendered page lets the user click either
button.

Each identified element is connected to the logic by `id`: `script.js` calls
`document.getElementById('<id>')` to read the **form inputs** and to write — then later re-read —
the **report-card placeholders**. For the exact function signatures and the full per-function
lists of reads, writes, side effects, and error behavior, see the function reference
[`script-js.md`](script-js.md); this page does not duplicate those contracts.

**DOM-read invariant.** `downloadPDF()` reads the **rendered** report-card placeholders
(`#rName`, `#rRoll`, `#totalMarks`, `#percentage`, `#grade`), **not** the form inputs
[Readme.md:L235-L239] — which is why **Generate Report** must run before **Download PDF**. The
invariant is defined in full in
[`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).

---

## Expected Behavior / Contract

This section states the **structural contract** the markup must satisfy for `script.js` to
function — the page's direct answer to *"what is the expectation from the code?"*.

### Required IDs (precondition)

Every `id` below **must exist in the DOM, spelled exactly as shown**, for the corresponding
`script.js` access to succeed. The seven form-input IDs are read by `generateReport()`
[Readme.md:L61-L68]; the six report-card IDs are written by `generateReport()` and re-read by
`downloadPDF()` [Readme.md:L74-L93].

| Required id | Group | Accessed by |
|---|---|---|
| `studentName` | form input | `generateReport()` read [Readme.md:L178] |
| `rollNumber` | form input | `generateReport()` read [Readme.md:L179] |
| `maths` | form input | `generateReport()` read [Readme.md:L182] |
| `science` | form input | `generateReport()` read [Readme.md:L183] |
| `english` | form input | `generateReport()` read [Readme.md:L184] |
| `history` | form input | `generateReport()` read [Readme.md:L185] |
| `computer` | form input | `generateReport()` read [Readme.md:L186] |
| `marksTable` | report card | `generateReport()` write [Readme.md:L191-L192], [Readme.md:L204] |
| `rName` | report card | `generateReport()` write [Readme.md:L223] / `downloadPDF()` read [Readme.md:L235] |
| `rRoll` | report card | `generateReport()` write [Readme.md:L224] / `downloadPDF()` read [Readme.md:L236] |
| `totalMarks` | report card | `generateReport()` write [Readme.md:L225] / `downloadPDF()` read [Readme.md:L237] |
| `percentage` | report card | `generateReport()` write [Readme.md:L226] / `downloadPDF()` read [Readme.md:L238] |
| `grade` | report card | `generateReport()` write [Readme.md:L227] / `downloadPDF()` read [Readme.md:L239] |

### Failure mode

There is **no defensive null-checking** anywhere in the code [Readme.md:L176-L253]. If any required
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
inline `onclick` handlers [Readme.md:L70-L71] to work. They are defined by `script.js`, which is
included at the end of `<body>` [Readme.md:L96], so the wiring holds for normal page loads.

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
