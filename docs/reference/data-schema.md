# Reference — Fixed Data Schema

## Purpose

This page is the **single source of truth (SSOT)** for every hard-coded, fixed parameter in
the Student Report Generator: the **subjects** read from the form, the **score bounds**
(subject count, per-subject maximum, and total denominator), and the **grade thresholds**.

The application exposes **no runtime configuration** — all of these values are
**compile-time constants** baked directly into the application source. There is no settings
screen, configuration file, environment variable, or runtime override for any of them. To
prevent drift, consumer documents — notably
[`../functionality/computation.md`](../functionality/computation.md) and
[`../functionality/grading.md`](../functionality/grading.md) — **link to this page rather than
duplicating its values** (AAP 0.5.5).

All content below is code-grounded: every value carries an inline `[Readme.md:Lx-Ly]`
citation back to the application source, which is embedded in the repository-root `Readme.md`.

## Source Location

- **Subjects:** [Readme.md:L166-L172]
- **Total denominator:** [Readme.md:L192]
- **Grade thresholds:** [Readme.md:L194-L206]

---

## Subjects

The application reads exactly **five** subjects — in the fixed source order shown below —
inside `generateReport()` [Readme.md:L166-L172]. Each value is read by element ID from a
matching `<input type="number">` field [Readme.md:L49-L53].

| Subject | Input ID | Type | Default (blank/invalid) |
|---|---|---|---|
| Maths | `#maths` [Readme.md:L49] | `number` | `0` [Readme.md:L167] |
| Science | `#science` [Readme.md:L50] | `number` | `0` [Readme.md:L168] |
| English | `#english` [Readme.md:L51] | `number` | `0` [Readme.md:L169] |
| History | `#history` [Readme.md:L52] | `number` | `0` [Readme.md:L170] |
| Computer | `#computer` [Readme.md:L53] | `number` | `0` [Readme.md:L171] |

Each mark is coerced with `parseInt(... .value || 0)`; the `|| 0` is the **no-NaN guard**, so
a blank or otherwise falsy `.value` becomes `0` *before* `parseInt` runs — which is why the
default for blank/invalid input is `0` [Readme.md:L167-L171]. This behavior belongs to feature
**F-002 (Marks Entry)**.

---

## Score Bounds

| Parameter | Value | Notes / Source |
|---|---|---|
| Number of subjects | `5` | Fixed set — Maths, Science, English, History, Computer [Readme.md:L166-L172] |
| Per-subject maximum | `100` | Intended domain expectation (500 ÷ 5). **Not enforced** — the code performs no input validation or clamping; see [behavioral contracts](../contracts/behavioral-contracts.md) |
| Total denominator | `500` | Divisor used to compute the percentage [Readme.md:L192] |

The percentage is computed from the fixed `500`-point denominator [Readme.md:L192]:

```javascript
const percentage = (total / 500) * 100;
```

**No input validation.** The code never checks that a mark is `≤ 100` or `≥ 0`
[Readme.md:L166-L192]. The per-subject maximum of `100` is therefore an *intended domain
expectation* (500 ÷ 5 subjects), **not** a constraint the application enforces: entering `250`
for a single subject is accepted and flows directly into `total`, which can push the computed
percentage above `100`. The full invariant discussion lives in
[behavioral contracts](../contracts/behavioral-contracts.md). This parameter group underpins
feature **F-004 (Percentage)**.

---

## Grade Thresholds

Grades are assigned by an ordered `if / else-if` cascade [Readme.md:L194-L206]. The ranges
below are listed highest to lowest.

| Grade | Percentage Range | Source |
|---|---|---|
| `A+` | ≥ 90 | [Readme.md:L196-L197] |
| `A` | ≥ 80 and < 90 | [Readme.md:L198-L199] |
| `B` | ≥ 70 and < 80 | [Readme.md:L200-L201] |
| `C` | ≥ 60 and < 70 | [Readme.md:L202-L203] |
| `D` | ≥ 50 and < 60 | [Readme.md:L204-L205] |
| `F` | < 50 (default) | [Readme.md:L194] |

The default grade `'F'` is initialized at [Readme.md:L194] and is retained whenever every
`else if` test fails (percentage below `50`). The upper bound of each band (the `< 90`,
`< 80`, … part) is **implicit**: it follows from the ordered cascade — a higher band is tested
first, so, for example, `A` applies only when the percentage is `>= 80` **and** the `>= 90`
test has already failed. There is no literal `< 90` comparison in the source. This is feature
**F-005 (Grade Assignment)**; the grade-decision flowchart lives in
[`../functionality/grading.md`](../functionality/grading.md).

---

## How It Works

Every value on this page is a **compile-time constant** embedded directly in the application
source — the `index.html` form and the `script.js` logic, both embedded in `Readme.md`
[Readme.md:L166-L206]. There is **no UI control, configuration file, environment variable, or
runtime override** for the subject set, the per-subject maximum, the `500`-point denominator,
or the grade thresholds. Changing any of these values would require editing the source code,
which is outside the scope of this documentation effort.

---

## Notes / Contract Pointer

These are the **authoritative fixed values** for the application — the single source of truth
that downstream documents link to rather than restate. For deeper behavioral detail:

- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the
  **determinism guarantee** (every percentage maps to exactly one grade) and the
  **no-input-validation** invariant (why the per-subject `100` maximum is not enforced).
- [`../functionality/grading.md`](../functionality/grading.md) — the consumer of the grade
  thresholds, including the grade-decision flowchart.
- [`../functionality/computation.md`](../functionality/computation.md) — the consumer of the
  `500`-point denominator and the percentage formula.

---

## Related Documents

- [Documentation Hub](../index.md) — back to the master table of contents.
- [Behavioral Contracts](../contracts/behavioral-contracts.md) — invariants, preconditions, and postconditions.
- [Grading](../functionality/grading.md) — grade-assignment functionality and decision flowchart.
- [Computation](../functionality/computation.md) — total and percentage computation.
