# Computation

## Purpose

This guide documents the **Computation** functional layer of the Student Report Generator — feature **F-003 Total Aggregation** and feature **F-004 Percentage**. It explains how `generateReport()` sums the five subject marks into a single `total` (F-003) and then derives the `percentage` against the fixed 500-point scale (F-004) [Readme.md:L174-L192]. All content is **code-grounded** in the application source embedded in the repository-root `Readme.md`; every technical claim carries an inline `[Readme.md:Lx-Ly]` citation. This is a documentation-only reference and modifies no source code.

## Source Location

The computation logic lives inside `generateReport()` in the `script.js` block embedded in `Readme.md`:

- **Total + percentage:** [Readme.md:L174-L192]
- **On-screen display — percentage precision (`.toFixed(2)`):** [Readme.md:L211]
- **On-screen display — unformatted total:** [Readme.md:L210]

---

## F-003 Total Aggregation

`generateReport()` initializes an accumulator to zero and then walks the `subjects` object with a `for...in` loop, adding each subject's marks to the running `total` [Readme.md:L174-L190]:

```javascript
let total = 0;
total += subjects[subject];
```

- `total` is initialized to `0` [Readme.md:L174] and accumulates each subject's numeric marks inside the `for (let subject in subjects)` loop [Readme.md:L179-L190], performing one `total += subjects[subject]` addition per iteration [Readme.md:L180].
- The same loop also appends a row to the marks table (`tableBody.innerHTML += row;` [Readme.md:L189]). That table-building step is a **rendering** concern (feature F-006) and is documented separately in [`report-rendering.md`](report-rendering.md); for the computation layer, only the `total += subjects[subject]` accumulation is relevant [Readme.md:L180].
- Because the **no-NaN guard** — the `|| 0` inside `parseInt(... .value || 0)` — guarantees every subject value is a number before the loop runs [Readme.md:L166-L172], `total` is always a finite number and never becomes `NaN` from a blank field. The guard itself belongs to the data-entry step; see [`data-entry.md`](data-entry.md) and [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).

The loop visits the subjects in the `subjects` object's insertion order — **Maths → Science → English → History → Computer** [Readme.md:L167-L171] — but iteration order does not affect the sum, because addition is commutative; `total` is the same regardless of order.

---

## F-004 Percentage

After the loop, the percentage is computed from `total` against the **fixed 500-point denominator** [Readme.md:L192]:

```javascript
const percentage = (total / 500) * 100;
```

The result is later written to the rendered DOM with two decimal places [Readme.md:L211]:

```javascript
document.getElementById('percentage').innerText = percentage.toFixed(2);
```

- The denominator `500` is a fixed constant [Readme.md:L192]. It is **owned by the data schema** — see [`../reference/data-schema.md`](../reference/data-schema.md) for the single-source-of-truth definition (five subjects × an intended 100-point maximum) — and is not restated as authoritative here.
- **Number vs. string (critical nuance).** Both `total` and the internal `percentage` are full-precision JavaScript **numbers** [Readme.md:L174] [Readme.md:L192]. Rounding happens **only at display time**: `.toFixed(2)` produces a **2-decimal string** that is written to `#percentage` [Readme.md:L211]. The stored `percentage` value itself is **not** rounded. Separately, `#totalMarks` is written **unformatted** — the raw numeric `total` with no `.toFixed()` applied [Readme.md:L210].
- The literal `%` sign shown after the number is **static markup** in `index.html` [Readme.md:L76], not produced by the JavaScript; `percentage.toFixed(2)` writes only the numeric portion into the `#percentage` span.

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

1. **Total (F-003):** `90 + 85 + 80 + 75 + 70 = 400` — each value is added by `total += subjects[subject]` [Readme.md:L180].
2. **Percentage (F-004):** `(400 / 500) * 100 = 80` [Readme.md:L192].
3. **Display:** `(80).toFixed(2)` renders as the string `"80.00"` in `#percentage` [Readme.md:L211], followed by the static `%` from the markup [Readme.md:L76]; `#totalMarks` shows the unformatted `400` [Readme.md:L210].
4. **Grade (downstream):** a percentage of `80` maps to grade **A** (`≥ 80`); see [`grading.md`](grading.md) for the full cascade.

**Edge example — all fields blank.** With every input left empty, the no-NaN guard coerces each subject to `0` [Readme.md:L166-L172], so `total = 0` [Readme.md:L180], `percentage = (0 / 500) * 100 = 0` [Readme.md:L192], displayed as `"0.00"` [Readme.md:L211], which maps to the default grade **F** (see [`grading.md`](grading.md)).

---

## Expected Behavior / Contract

This block states the explicit expectation from the computation code.

| Aspect | Contract |
| --- | --- |
| **Inputs** | The five numeric subject values held in the `subjects` object — Maths, Science, English, History, Computer [Readme.md:L166-L172]. Each is guaranteed numeric by the upstream no-NaN guard (`\|\| 0`) [Readme.md:L167-L171]. |
| **Total (F-003)** | `total` = the arithmetic sum of the five subject values, accumulated by `total += subjects[subject]` across the `for...in` loop [Readme.md:L179-L190]. Always a finite number; never `NaN` for blank fields. |
| **Percentage (F-004)** | `percentage = (total / 500) * 100` [Readme.md:L192]. The denominator is **fixed at 500** — single source of truth: [`../reference/data-schema.md`](../reference/data-schema.md). |
| **Precision / display** | The internal `percentage` is a **full-precision number** [Readme.md:L192]; the DOM shows it via `.toFixed(2)` as a **2-decimal string** [Readme.md:L211]. `#totalMarks` is shown **unformatted** (the raw number) [Readme.md:L210]. The stored values are **not** rounded. |
| **No validation / clamping** | Marks are never range-checked or clamped. Out-of-range marks (negative, or greater than 100) flow straight into `total` and can yield a percentage outside `[0, 100]` [Readme.md:L166-L192]. The full invariant discussion lives in [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md). |
| **Determinism** | Identical inputs always produce an identical `total` and `percentage` — there is no randomness, time dependency, or persisted state [Readme.md:L174-L192]. |
| **Side effects** | The computation arithmetic is **pure** — it only reads the `subjects` values and produces `total`/`percentage`. The resulting values are written to the rendered DOM by the rendering step [Readme.md:L210-L211], documented in [`report-rendering.md`](report-rendering.md). |

---

## Related Documents

- [`../reference/data-schema.md`](../reference/data-schema.md) — the fixed **500 denominator** and the per-subject schema (single source of truth).
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the `.toFixed(2)` display-precision invariant and the determinism guarantee.
- [`data-entry.md`](data-entry.md) — the upstream step that builds the `subjects` object and applies the no-NaN guard.
- [`grading.md`](grading.md) — the downstream step that maps `percentage` to a letter grade.
- [`report-rendering.md`](report-rendering.md) — where `total` and `percentage` are written to the rendered DOM.
- [`../api-reference/script-js.md`](../api-reference/script-js.md) — exact function signatures and DOM read/write contracts for `generateReport()`.
- [`../index.md`](../index.md) — back to the Documentation Hub.
