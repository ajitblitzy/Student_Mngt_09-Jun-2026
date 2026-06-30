# Data Entry

The **Data Entry** layer is how the Student Report Generator collects everything it needs from the user before any computation runs. It groups two features — **F-001 Identity Capture** and **F-002 Marks Entry** — both of which read their values from the **form inputs** rendered on the page.

## Purpose

This guide documents the **Data Entry** functional layer of the Student Report Generator: **F-001 Identity Capture** (student name and roll number) and **F-002 Marks Entry** (the five subject marks). It explains how the application collects these seven values from the **form inputs** and hands them to `generateReport()` for processing. All content is code-grounded in the application source embedded in the repository-root `Readme.md`.

## Source Location

| Concern | Embedded source | Citation |
|---|---|---|
| Form input markup (identity + marks) | `index.html` | [Readme.md:L61-L68] |
| Logic that reads the inputs | `script.js` → `generateReport()` | [Readme.md:L177-L187] |

> The entire application — `index.html`, `style.css`, and `script.js` — is embedded as fenced code blocks inside `Readme.md`; the standalone files implied by the README project tree do not physically exist, so `Readme.md` is the sole source of truth.

---

## F-001 Identity Capture

F-001 captures the student's **identity**: a name and a roll number. The markup declares two `<input type="text">` fields — `#studentName` [Readme.md:L61] and `#rollNumber` [Readme.md:L62] — inside the form section [Readme.md:L61-L62]:

```html
<input type="text" id="studentName" placeholder="Student Name">
<input type="text" id="rollNumber" placeholder="Roll Number">
```

When the user clicks **Generate Report**, `generateReport()` reads both fields as **raw strings** through the `.value` property [Readme.md:L178-L179]:

```javascript
const name = document.getElementById('studentName').value;
const roll = document.getElementById('rollNumber').value;
```

These identity values are used **verbatim** — there is no `parseInt`, no trimming, and no validation. Whatever the user typed (including an empty string) flows straight through to the rendered report card and, later, into the exported PDF. This is the key distinction from F-002: identity values stay **strings**, whereas marks are coerced to **numbers**.

---

## F-002 Marks Entry

F-002 captures the marks for the five fixed subjects. The markup declares five `<input type="number">` fields — `#maths`, `#science`, `#english`, `#history`, and `#computer`, in this source order [Readme.md:L64-L68]:

```html
<input type="number" id="maths" placeholder="Maths Marks">
<input type="number" id="science" placeholder="Science Marks">
<input type="number" id="english" placeholder="English Marks">
<input type="number" id="history" placeholder="History Marks">
<input type="number" id="computer" placeholder="Computer Marks">
```

`generateReport()` reads each field into a `subjects` object [Readme.md:L181-L187], coercing every value with the **no-NaN guard** `parseInt(document.getElementById('<id>').value || 0)`. The `Maths` entry below is representative; the other four subjects follow the identical pattern [Readme.md:L182]:

```javascript
Maths: parseInt(document.getElementById('maths').value || 0),
```

The `|| 0` is applied to the **`.value`** *inside* `parseInt`: a blank number-input has `.value === ""` (falsy), which becomes `0` **before** `parseInt` runs, so an empty field contributes `0` rather than `NaN`. This is the **no-NaN guard**. It guards **only** against blank/falsy input — it does **not** clamp negatives and does **not** cap values at `100`; there is no range validation anywhere in the source (see the **Expected Behavior / Contract** section below).

| Subject | Input ID | Type | Default when blank |
|---|---|---|---|
| Maths | `#maths` | `number` | `0` |
| Science | `#science` | `number` | `0` |
| English | `#english` | `number` | `0` |
| History | `#history` | `number` | `0` |
| Computer | `#computer` | `number` | `0` |

> The canonical fixed schema — the five subjects, the (unenforced) per-subject maximum of `100`, and the blank-field defaults — is owned by [`../reference/data-schema.md`](../reference/data-schema.md). This guide links to it rather than restating it as authoritative.

---

## Expected Behavior / Contract

This section is the explicit **expectation from the code** for the Data Entry step. Every row is grounded in the cited source.

| Aspect | Expectation | Source |
|---|---|---|
| **Inputs** | Two identity strings (`#studentName`, `#rollNumber`) plus five numeric marks (`#maths`, `#science`, `#english`, `#history`, `#computer`). | [Readme.md:L61-L68] |
| **Acceptance (no validation)** | All inputs are accepted **as-is**. Identity strings are not validated, trimmed, or required. | [Readme.md:L178-L179] |
| **Marks not range-checked** | Marks are read and coerced to numbers but are never range-checked. | [Readme.md:L181-L187] |
| **Blank/falsy marks → `0`** | A blank or otherwise **falsy** `.value` defaults to `0` *before* `parseInt` runs (the no-NaN guard), so an empty field contributes `0` rather than `NaN`. There is no validation, trimming, or clamping — a **truthy non-numeric** `.value` (e.g. `"abc"`) is **not** guarded and would `parseInt` to `NaN`. | [Readme.md:L182-L186] |
| **No clamping** | Negative values and values greater than `100` are accepted **unchanged**; there is no min/max enforcement. The *intended* per-subject maximum of `100` (see [`../reference/data-schema.md`](../reference/data-schema.md)) is **not** enforced. | [Readme.md:L181-L187] |
| **Preconditions** | The seven input element IDs must exist in the DOM exactly as spelled (see [`../api-reference/html-structure.md`](../api-reference/html-structure.md)); a missing ID would cause a `null` read and a `TypeError` downstream. | [Readme.md:L61-L68], [Readme.md:L177-L187] |
| **Side effects** | None in the data-entry step itself — values are only **read** here; the table rebuild, computation, and rendering happen later in `generateReport()`. | [Readme.md:L177-L187] |
| **Outputs (postcondition)** | `name` and `roll` captured as strings, and a `subjects` object with five entries — each the result of `parseInt(.value || 0)` — ready for computation. | [Readme.md:L178-L187] |

**Most commonly misunderstood point:** the `|| 0` guard's scope is the `.value` only. It prevents `NaN` from blank fields but performs **no** range validation — negatives and values above `100` pass through untouched. The full invariant catalog, including this no-NaN guard, is the single source of truth in [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).

---

## Related Documents

- [`../api-reference/html-structure.md`](../api-reference/html-structure.md) — the DOM element/ID reference for these form inputs and their wiring.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the no-NaN guard invariant and the full set of preconditions (single source of truth).
- [`../reference/data-schema.md`](../reference/data-schema.md) — the fixed subject schema and the (unenforced) per-subject maximum of `100`.
- [`computation.md`](computation.md) — the next step (**F-003** total + **F-004** percentage) that consumes the `subjects` object.
- [`../index.md`](../index.md) — back to the Documentation Hub.

---

*Maintenance note: this guide is derived from the application source embedded in `Readme.md` [Readme.md:L61-L187]. If that embedded code changes, update the cited line ranges and contract statements here so the documentation stays accurate.*
