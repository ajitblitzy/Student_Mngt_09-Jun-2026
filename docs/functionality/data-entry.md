# Data Entry

## Purpose

This guide documents the **Data Entry** functional layer of the Student Report
Generator — feature **F-001 Identity Capture** and feature **F-002 Marks Entry**.
Together these features describe how the application collects the student name, roll
number, and five subject marks from the **form inputs** before any computation or
rendering takes place. All content is code-grounded in the application source embedded
in the repository-root `Readme.md`.

---

## Source Location

The data-entry surface spans two parts of the embedded source:

- **Markup** — the **form inputs** that capture user data, defined in the embedded
  `index.html` block [Readme.md:L61-L68].
- **Logic** — the reads that pull those values into `generateReport()`, defined in the
  embedded `script.js` block [Readme.md:L177-L187].

---

## F-001 Identity Capture

F-001 captures the student's identity through two free-text **form inputs**:
`#studentName` [Readme.md:L61] and `#rollNumber` [Readme.md:L62].

```html
<input type="text" id="studentName" placeholder="Student Name">
<input type="text" id="rollNumber" placeholder="Roll Number">
```

When **Generate Report** is clicked, `generateReport()` reads these two fields as **raw
strings** via `.value`, with no parsing, trimming, or validation applied
[Readme.md:L178-L179]:

```javascript
const name = document.getElementById('studentName').value;
const roll = document.getElementById('rollNumber').value;
```

The captured `name` and `roll` are used verbatim. Unlike the marks (F-002), they are
**not** passed through `parseInt` or any numeric coercion — whatever the user types
(including an empty string) is accepted as-is and later written straight to the
**rendered DOM** report card.

---

## F-002 Marks Entry

F-002 captures marks for five subjects through five `<input type="number">` fields —
`#maths`, `#science`, `#english`, `#history`, and `#computer`, in this source order
[Readme.md:L64-L68]:

```html
<input type="number" id="maths" placeholder="Maths Marks">
<input type="number" id="science" placeholder="Science Marks">
<input type="number" id="english" placeholder="English Marks">
<input type="number" id="history" placeholder="History Marks">
<input type="number" id="computer" placeholder="Computer Marks">
```

`generateReport()` reads each field while applying the **no-NaN guard** — the `|| 0`
placed **inside** `parseInt`, operating on `.value` — and assembles the results into a
`subjects` object [Readme.md:L181-L187]. The `Maths` line is representative of all five
[Readme.md:L182]:

```javascript
Maths: parseInt(document.getElementById('maths').value || 0),
```

The same guard form is applied identically to each of the five subjects. Note the exact
placement: the `|| 0` defaults a blank or otherwise falsy `.value` to `0` **before**
`parseInt` runs — it is `parseInt(... .value || 0)`, **not** `parseInt(value) || 0`. An
empty number field has `.value === ""` (falsy), which becomes `0`, so blank fields
contribute `0` rather than `NaN`. The `<input type="number">` control already restricts
most non-numeric typing, but a cleared field still yields `""`; the guard handles that
case. This guard prevents `NaN` **only** for a blank/falsy `.value` — it is **not** a
general invalid-input validator (it adds no post-`parseInt` fallback, so an arbitrary
non-empty invalid string would still parse to `NaN`) and it does **not** validate ranges
(see the Expected Behavior / Contract below).

| Subject | Input ID | Type | Default when blank |
|---|---|---|---|
| Maths | `#maths` | `number` | `0` |
| Science | `#science` | `number` | `0` |
| English | `#english` | `number` | `0` |
| History | `#history` | `number` | `0` |
| Computer | `#computer` | `number` | `0` |

The canonical fixed schema — the five subjects, the per-subject maximum of `100`, and
default values — is owned by [`../reference/data-schema.md`](../reference/data-schema.md);
this guide links to it rather than restating it as authoritative.

---

## Expected Behavior / Contract

This section is the explicit statement of *what the data-entry code is expected to do* —
the direct answer to "highlight what is the expectation from the code." Every row is
grounded in the cited source.

| Aspect | Expectation | Source |
|---|---|---|
| **Inputs** | Two identity strings (`#studentName`, `#rollNumber`) plus five numeric marks (`#maths`, `#science`, `#english`, `#history`, `#computer`). | [Readme.md:L61-L68] |
| **Acceptance (no validation)** | All inputs are accepted **as-is**. Identity strings are **not** validated, trimmed, or required; marks are **not** range-checked. | [Readme.md:L178-L179], [Readme.md:L181-L187] |
| **Blank/falsy marks → `0`** | The **no-NaN guard** `parseInt(... .value \|\| 0)` defaults a blank or otherwise falsy `.value` (e.g. `""`) to `0` *before* `parseInt` runs, so a blank/falsy field contributes `0` rather than `NaN`. The guard defaults only blank/falsy `.value` values; it is **not** a general invalid-input validator and adds no post-`parseInt` fallback, so an arbitrary non-empty invalid string would still parse to `NaN`. | [Readme.md:L182-L186] |
| **No clamping** | Negative values and values greater than `100` are accepted unchanged — there is **no** min/max enforcement. The *intended* per-subject maximum is `100` (see [`../reference/data-schema.md`](../reference/data-schema.md)) but it is **not** enforced. | [Readme.md:L181-L187] |
| **Preconditions** | The seven input element IDs must exist in the DOM exactly as spelled (see [`../api-reference/html-structure.md`](../api-reference/html-structure.md)). A missing ID would make `getElementById` return `null`, causing a `TypeError` downstream; the full precondition detail lives in [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md). | [Readme.md:L61-L68] |
| **Side effects** | **None** in the data-entry step itself — the values are only *read* here. Computation and rendering happen later within the same `generateReport()` call. | [Readme.md:L177-L187] |
| **Outputs (postcondition)** | The identity strings are captured into the local `name` and `roll` variables, and a `subjects` object of five numeric values is ready for computation. | [Readme.md:L178-L187] |

---

## Related Documents

- [HTML Structure Reference](../api-reference/html-structure.md) — the DOM element/ID
  reference for these **form inputs**.
- [Behavioral Contracts](../contracts/behavioral-contracts.md) — the single source of
  truth for the **no-NaN guard** invariant and the full precondition catalog.
- [Data Schema](../reference/data-schema.md) — the fixed subject schema and the
  (unenforced) per-subject maximum.
- [Computation](computation.md) — the next step (F-003 total, F-004 percentage) that
  consumes the `subjects` object.
- [Documentation Hub](../index.md) — back to the master table of contents.
