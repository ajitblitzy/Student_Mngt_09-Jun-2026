# Computation

## Purpose

This guide documents the **Computation** functional layer of the Student Report Generator — feature **F-003 Total Aggregation** and feature **F-004 Percentage**. It explains how `generateReport()` sums the five subject marks into a single `total` (F-003) and then derives the `percentage` against the fixed 500-point scale (F-004) [Readme.md:L189-L207]. All content is **code-grounded** in the application source embedded in the repository-root `Readme.md`; every technical claim carries an inline `[Readme.md:Lx-Ly]` citation. This is a documentation-only reference and modifies no source code.

## Source Location

The computation logic lives inside `generateReport()` in the `script.js` block embedded in `Readme.md`:

- **Total + percentage:** [Readme.md:L189-L207]
- **On-screen display — percentage precision (`.toFixed(2)`):** [Readme.md:L226]
- **On-screen display — unformatted total:** [Readme.md:L225]

---

## F-003 Total Aggregation

`generateReport()` initializes an accumulator to zero and then walks the `subjects` object with a `for...in` loop, adding each subject's marks to the running `total` [Readme.md:L189-L205]:

```javascript
let total = 0;
total += subjects[subject];
```

- `total` is initialized to `0` [Readme.md:L189] and accumulates each subject's numeric marks inside the `for (let subject in subjects)` loop [Readme.md:L194-L205], performing one `total += subjects[subject]` addition per iteration [Readme.md:L195].
- The same loop also appends a row to the marks table (`tableBody.innerHTML += row;` [Readme.md:L204]). That table-building step is a **rendering** concern (feature F-006) and is documented separately in [`report-rendering.md`](report-rendering.md); for the computation layer, only the `total += subjects[subject]` accumulation is relevant [Readme.md:L195].
- The **no-NaN guard** — the `|| 0` inside `parseInt(... .value || 0)` — defaults a blank or otherwise falsy `.value` to `0` **before** `parseInt` runs [Readme.md:L181-L187], so a blank field contributes `0` rather than `NaN` to `total`. It is **not** general input validation: a **truthy non-numeric** `.value` (for example `"abc"`) is **not** guarded and would `parseInt` to `NaN`, which would then propagate into `total`. The guard itself belongs to the data-entry step; see [`data-entry.md`](data-entry.md) and [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).

The loop visits the subjects in the `subjects` object's insertion order — **Maths → Science → English → History → Computer** [Readme.md:L182-L186] — but iteration order does not affect the sum, because addition is commutative; `total` is the same regardless of order.

---

## F-004 Percentage

After the loop, the percentage is computed from `total` against the **fixed 500-point denominator** [Readme.md:L207]:

```javascript
const percentage = (total / 500) * 100;
```

The result is later written to the rendered DOM with two decimal places [Readme.md:L226]:

```javascript
document.getElementById('percentage').innerText = percentage.toFixed(2);
```

- The denominator `500` is a fixed constant [Readme.md:L207]. It is **owned by the data schema** — see [`../reference/data-schema.md`](../reference/data-schema.md) for the single-source-of-truth definition (five subjects × an intended 100-point maximum) — and is not restated as authoritative here.
- **Number vs. string (critical nuance).** Both `total` and the internal `percentage` are full-precision JavaScript **numbers** [Readme.md:L189] [Readme.md:L207]. Rounding happens **only at display time**: `.toFixed(2)` produces a **2-decimal string** that is written to `#percentage` [Readme.md:L226]. The stored `percentage` value itself is **not** rounded. Separately, `#totalMarks` is written **unformatted** — the raw numeric `total` with no `.toFixed()` applied [Readme.md:L225].
- The literal `%` sign shown after the number is **static markup** in `index.html` [Readme.md:L91], not produced by the JavaScript; `percentage.toFixed(2)` writes only the numeric portion into the `#percentage` span.

---

## Worked Example

The following example is **illustrative**. It uses the same marks as the function reference in [`../api-reference/script-js.md`](../api-reference/script-js.md) for cross-document consistency; the grade derivation is owned by [`grading.md`](grading.md).

| Subject | Marks |
| --- | --- |
| Maths | 90 |
| Science | 85 |
| English | 80 |
| History | 75 |
| Computer | 70 |

1. **Total (F-003):** `90 + 85 + 80 + 75 + 70 = 400` — each value is added by `total += subjects[subject]` [Readme.md:L195].
2. **Percentage (F-004):** `(400 / 500) * 100 = 80` [Readme.md:L207].
3. **Display:** `(80).toFixed(2)` renders as the string `"80.00"` in `#percentage` [Readme.md:L226], followed by the static `%` from the markup [Readme.md:L91]; `#totalMarks` shows the unformatted `400` [Readme.md:L225].
4. **Grade (downstream):** a percentage of `80` maps to grade **A** (`≥ 80`); see [`grading.md`](grading.md) for the full cascade.

**Edge example — all fields blank.** With every input left empty, the no-NaN guard coerces each subject to `0` [Readme.md:L181-L187], so `total = 0` [Readme.md:L195], `percentage = (0 / 500) * 100 = 0` [Readme.md:L207], displayed as `"0.00"` [Readme.md:L226], which maps to the default grade **F** (see [`grading.md`](grading.md)).

---

## Expected Behavior / Contract

This block states the explicit expectation from the computation code.

| Aspect | Contract |
| --- | --- |
| **Inputs** | The five subject values held in the `subjects` object — Maths, Science, English, History, Computer [Readme.md:L181-L187]. The upstream `\|\| 0` guard defaults blank/falsy fields to `0` so empty fields do not yield `NaN`; it is **not** general validation, and a truthy non-numeric value can still parse to `NaN` [Readme.md:L182-L186]. |
| **Total (F-003)** | `total` = the arithmetic sum of the five subject values, accumulated by `total += subjects[subject]` across the `for...in` loop [Readme.md:L194-L205]. Blank fields contribute `0` (never `NaN`); there is no validation for truthy non-numeric values, which would propagate `NaN` into `total`. |
| **Percentage (F-004)** | `percentage = (total / 500) * 100` [Readme.md:L207]. The denominator is **fixed at 500** — single source of truth: [`../reference/data-schema.md`](../reference/data-schema.md). |
| **Precision / display** | The internal `percentage` is a **full-precision number** [Readme.md:L207]; the DOM shows it via `.toFixed(2)` as a **2-decimal string** [Readme.md:L226]. `#totalMarks` is shown **unformatted** (the raw number) [Readme.md:L225]. The stored values are **not** rounded. |
| **No validation / clamping** | Marks are never range-checked or clamped. Out-of-range marks (negative, or greater than 100) flow straight into `total` and can yield a percentage outside `[0, 100]` [Readme.md:L181-L207]. The full invariant discussion lives in [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md). |
| **Determinism** | Identical inputs always produce an identical `total` and `percentage` — there is no randomness, time dependency, or persisted state [Readme.md:L189-L207]. |
| **Side effects** | The computation arithmetic is **pure** — it only reads the `subjects` values and produces `total`/`percentage`. The resulting values are written to the rendered DOM by the rendering step [Readme.md:L225-L226], documented in [`report-rendering.md`](report-rendering.md). |

---

## Related Documents

- [`../reference/data-schema.md`](../reference/data-schema.md) — the fixed **500 denominator** and the per-subject schema (single source of truth).
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the `.toFixed(2)` display-precision invariant and the determinism guarantee.
- [`data-entry.md`](data-entry.md) — the upstream step that builds the `subjects` object and applies the no-NaN guard.
- [`grading.md`](grading.md) — the downstream step that maps `percentage` to a letter grade.
- [`report-rendering.md`](report-rendering.md) — where `total` and `percentage` are written to the rendered DOM.
- [`../api-reference/script-js.md`](../api-reference/script-js.md) — exact function signatures and DOM read/write contracts for `generateReport()`.
- [`../index.md`](../index.md) — back to the Documentation Hub.
