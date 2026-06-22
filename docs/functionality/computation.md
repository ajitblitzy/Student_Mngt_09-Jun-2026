# Computation

## Purpose

This guide documents the **Computation** functional layer of the Student Report Generator: how the
application aggregates the five subject marks into a `total` (feature **F-003, Total Aggregation**)
and derives the `percentage` out of a fixed 500-point denominator (feature **F-004, Percentage**).
Both steps execute inside `generateReport()` and run on every report generation.

All content on this page is code-grounded — it is extracted from the `script.js` block embedded in
the repository-root `Readme.md`, and every technical claim carries an inline `Readme.md` line-range
citation.

---

## Source Location

- **Total aggregation and percentage formula:** [Readme.md:L189-L207]
- **On-screen display precision (percentage):** [Readme.md:L226] (the unformatted total is written at [Readme.md:L225])

The computation reads the `subjects` object built earlier in `generateReport()` [Readme.md:L181-L187]
and writes its results to the rendered DOM at the end of the same function [Readme.md:L225-L226].

---

## How It Works

Both computation steps run inside `generateReport()`, in sequence, on every report generation, operating on the `subjects` object built earlier in the same function under the `parseInt(... .value || 0)` no-NaN guard [Readme.md:L181-L187]. First, **F-003 (Total Aggregation)** initializes a `total` accumulator to `0` [Readme.md:L189] and sums the five subject values in a `for...in` loop over `subjects` [Readme.md:L194-L205]. Then **F-004 (Percentage)** derives `percentage = (total / 500) * 100` from that total over the fixed 500-point denominator [Readme.md:L207]. Finally the results are written to the rendered DOM: `total` is shown **unformatted** [Readme.md:L225] while `percentage` is displayed with two-decimal precision via `.toFixed(2)` [Readme.md:L226]. The two steps are detailed below.

---

## F-003 Total Aggregation

The total is the sum of the five subject marks. A `total` accumulator is initialized to `0`
[Readme.md:L189]:

```javascript
let total = 0;
```

Each subject's numeric value is then added to it inside a `for...in` loop over the `subjects`
object [Readme.md:L194-L205]; the per-subject accumulation statement is [Readme.md:L195]:

```javascript
total += subjects[subject];
```

- `total` starts at `0` [Readme.md:L189] and is incremented once per subject by
  `total += subjects[subject];` [Readme.md:L195], so after the loop it holds the arithmetic sum of
  all five marks [Readme.md:L194-L205].
- Because the upstream **no-NaN guard** — `parseInt(... .value || 0)` — defaults every blank or
  otherwise falsy mark field to `0` *before* the value enters the `subjects` object [Readme.md:L181-L187],
  an empty field contributes `0` to the sum rather than `NaN`. The guard defaults only blank/falsy
  values; it does not validate arbitrary non-empty input. (The guard itself is documented in
  [`data-entry.md`](data-entry.md) and
  [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).)
- The same `for...in` loop also appends a row to the marks table (`tableBody.innerHTML += row;`
  [Readme.md:L204]). That is a **rendering** concern, not a computation one, and is documented
  separately in [`report-rendering.md`](report-rendering.md). For this layer, only the
  `total += subjects[subject]` accumulation [Readme.md:L195] matters.

---

## F-004 Percentage

Once the loop has produced `total`, the percentage is a single arithmetic expression over the fixed
**500**-point denominator [Readme.md:L207]:

```javascript
const percentage = (total / 500) * 100;
```

The computed value is then written to the report card with two-decimal display precision
[Readme.md:L226]:

```javascript
document.getElementById('percentage').innerText = percentage.toFixed(2);
```

- The denominator **500** is a fixed, hard-coded constant [Readme.md:L207]. It is owned by the schema
  reference — see [`../reference/data-schema.md`](../reference/data-schema.md) for the authoritative
  value and its `5 subjects × 100` rationale; this page links to it rather than restating it.
- **Number-vs-string nuance (important):** `total` and the internal `percentage` are full-precision
  JavaScript **numbers** [Readme.md:L189, L207]. Only the *displayed* `#percentage` value is a
  2-decimal **string** produced by `.toFixed(2)` [Readme.md:L226]. The stored values are **not**
  rounded.

> **Note — `#totalMarks` is displayed unformatted.** The total is written straight to the DOM with
> `document.getElementById('totalMarks').innerText = total;` [Readme.md:L225] — no `.toFixed()` and no
> formatting. Only the percentage is formatted to two decimals [Readme.md:L226].

> **Note — the `%` sign is static markup.** The literal percent sign shown after the value comes from
> the HTML (`<span id="percentage"></span>%` [Readme.md:L91]), not from `generateReport()`. The
> JavaScript writes only the numeric 2-decimal string into the `#percentage` span.

---

## Worked Example

The following example is **illustrative** — it traces a representative set of marks through F-003 and
F-004. (The same figures are used in [`../api-reference/script-js.md`](../api-reference/script-js.md)
for cross-document consistency.)

| Subject | Marks |
|---|---|
| Maths | 90 |
| Science | 85 |
| English | 80 |
| History | 75 |
| Computer | 70 |

Step-by-step:

1. **Total (F-003):** `90 + 85 + 80 + 75 + 70 = 400`, accumulated by `total += subjects[subject];`
   [Readme.md:L195].
2. **Percentage (F-004):** `(400 / 500) * 100 = 80`, from `const percentage = (total / 500) * 100;`
   [Readme.md:L207].
3. **Display:** `(80).toFixed(2)` renders as `80.00` in the `#percentage` span [Readme.md:L226], while
   `#totalMarks` shows `400` unformatted [Readme.md:L225].
4. **Grade (downstream):** a percentage of `80` maps to grade **A** (the `>= 80` band) — the grade
   decision belongs to [`grading.md`](grading.md), not this computation layer.

**Edge example — all fields blank.** With every input empty, the no-NaN guard makes each subject `0`
[Readme.md:L181-L187], so `total = 0` [Readme.md:L195], `percentage = (0 / 500) * 100 = 0`, displayed
as `0.00` [Readme.md:L226]; the resulting grade is the default **F** (see [`grading.md`](grading.md)).

---

## Expected Behavior / Contract

| Aspect | Expectation |
|---|---|
| **Inputs** | The five numeric subject values held in the `subjects` object [Readme.md:L181-L187]. |
| **Total (F-003)** | `total` is the arithmetic sum of the five values, accumulated in the `for...in` loop [Readme.md:L194-L205]; it is numeric and computed deterministically. |
| **Percentage (F-004)** | `percentage = (total / 500) * 100` [Readme.md:L207]. The denominator is **fixed at 500** — see [`../reference/data-schema.md`](../reference/data-schema.md) (single source of truth). |
| **Precision / display** | The internal `percentage` is a full-precision number; the DOM shows `percentage.toFixed(2)` as a 2-decimal **string** [Readme.md:L226]. `total` is shown **unformatted** [Readme.md:L225]. Stored values are not rounded. |
| **No validation / clamping** | The code performs no range checking. Negative or `> 100` marks flow straight into `total`, so the percentage can fall **outside `[0, 100]`** [Readme.md:L181-L207]. The full invariant discussion lives in [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md). |
| **Determinism** | Identical inputs always yield identical `total` and `percentage` — the computation uses no randomness, clock, or persisted state. |
| **Side effects** | The arithmetic itself is pure; the resulting values are written to the **rendered DOM** by the rendering step [Readme.md:L225-L226], documented in [`report-rendering.md`](report-rendering.md). |

---

## Related Documents

- [`../reference/data-schema.md`](../reference/data-schema.md) — the fixed **500** denominator and per-subject schema (single source of truth).
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the `.toFixed(2)` precision invariant, the no-validation behavior, and the determinism guarantee.
- [`data-entry.md`](data-entry.md) — the upstream step (and no-NaN guard) that produces the `subjects` object.
- [`grading.md`](grading.md) — the downstream step that maps `percentage` to a letter grade.
- [`report-rendering.md`](report-rendering.md) — where `total` and `percentage` are written to the rendered DOM.
- [Documentation Hub](../index.md) — back to the master table of contents.
