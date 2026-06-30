# Reference — Fixed Data Schema

> **Single Source of Truth (SSOT).** This page is the canonical, authoritative reference for every hard-coded, fixed parameter in the Student Report Generator. All other documents link here instead of restating these values, so the schema is defined in exactly one place and cannot drift.

## Purpose

The Student Report Generator exposes **no runtime configuration**: there is no settings screen, configuration file, environment variable, or runtime override for any of its parameters. Every parameter — the set of subjects, the score bounds, and the grade thresholds — is a **compile-time constant** baked directly into the application source [Readme.md:L181-L221].

This document is the **single source of truth** for those fixed parameters (subjects, score bounds, grade thresholds). Per the documentation plan's hub-and-spoke model, downstream documents — especially [`../functionality/computation.md`](../functionality/computation.md) and [`../functionality/grading.md`](../functionality/grading.md) — **link to this page rather than duplicate** the values, which prevents the schema from drifting between documents.

All content below is **code-grounded**: every value and claim carries an inline `[Readme.md:Lx-Ly]` citation pointing at the embedded source. (The entire application source — `index.html`, `style.css`, and `script.js` — is embedded inside the repository-root `Readme.md`; the standalone files implied by the README's project tree do not physically exist, so `Readme.md` is the sole source.)

## Source Location

All values on this page are extracted from the application source embedded in `Readme.md`:

- **Subjects** — the five `parseInt` reads in `generateReport()`: [Readme.md:L181-L187]
- **Score bounds** — the `(total / 500) * 100` denominator: [Readme.md:L207]
- **Grade thresholds** — the `if/else-if` cascade: [Readme.md:L209-L221]

---

## Subjects

The application reads exactly **five subjects**, in one fixed order, inside `generateReport()` [Readme.md:L181-L187]. Each subject is read from its own HTML `<input type="number">` element by ID [Readme.md:L64-L68] and coerced to an integer with `parseInt(... .value || 0)`.

| Subject | Input ID | Type | Default (blank/falsy) |
| --- | --- | --- | --- |
| Maths | `#maths` [Readme.md:L64] | `number` (HTML input; parsed via `parseInt`) [Readme.md:L182] | `0` |
| Science | `#science` [Readme.md:L65] | `number` (HTML input; parsed via `parseInt`) [Readme.md:L183] | `0` |
| English | `#english` [Readme.md:L66] | `number` (HTML input; parsed via `parseInt`) [Readme.md:L184] | `0` |
| History | `#history` [Readme.md:L67] | `number` (HTML input; parsed via `parseInt`) [Readme.md:L185] | `0` |
| Computer | `#computer` [Readme.md:L68] | `number` (HTML input; parsed via `parseInt`) [Readme.md:L186] | `0` |

The subjects are read in the exact source order **Maths → Science → English → History → Computer** [Readme.md:L182-L186], and that same order is preserved when the marks table is rendered.

The `0` in the **Default (blank/falsy)** column comes from the **no-NaN guard** — the `|| 0` inside `parseInt(... .value || 0)` [Readme.md:L182-L186]. A blank or otherwise **falsy** `.value` becomes `0` *before* `parseInt` runs, so an empty field contributes `0` rather than `NaN`. This guards **only** blank/falsy values; it is **not** general input validation — a **truthy non-numeric** `.value` (for example `"abc"`) is **not** defaulted by `|| 0` and would `parseInt` to `NaN`. The most precise statement of this scope is the marks-entry note in [`../functionality/data-entry.md`](../functionality/data-entry.md). This is the data-entry default for feature **F-002 (Marks Entry)**.

---

## Score Bounds

| Parameter | Value | Notes / Source |
| --- | --- | --- |
| Number of subjects | `5` | The `subjects` object holds exactly five keys [Readme.md:L181-L187] |
| Per-subject maximum | `100` | **Intended** domain expectation = 500 ÷ 5; **NOT enforced — there is no input validation or clamping in the code** (see [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md)). Derived from the 500-point total [Readme.md:L207] |
| Total denominator | `500` | The fixed denominator in `percentage = (total / 500) * 100` [Readme.md:L207] |

The percentage is computed from the fixed 500-point denominator [Readme.md:L207]:

```javascript
const percentage = (total / 500) * 100;
```

**No input validation.** The code never checks that a mark is `≤ 100` or `≥ 0`; there is no upper- or lower-bound check anywhere in the source [Readme.md:L181-L207]. The per-subject maximum of `100` is therefore the *intended* schema (500-point total ÷ 5 subjects), **not** a constraint the code enforces — entering `250` for a single subject is accepted as-is and flows straight into `total`, which can push the computed percentage above 100. This denominator underpins feature **F-004 (Percentage)**; the full invariant discussion lives in [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md).

---

## Grade Thresholds

The grade is assigned by an ordered `if/else-if` cascade over `percentage`, with the grade variable initialized to `'F'` before the cascade runs [Readme.md:L209-L221].

| Grade | Percentage Range | Source |
| --- | --- | --- |
| `A+` | `≥ 90` | [Readme.md:L211-L212] |
| `A` | `≥ 80 and < 90` | [Readme.md:L213-L214] |
| `B` | `≥ 70 and < 80` | [Readme.md:L215-L216] |
| `C` | `≥ 60 and < 70` | [Readme.md:L217-L218] |
| `D` | `≥ 50 and < 60` | [Readme.md:L219-L220] |
| `F` | `< 50` (default) | [Readme.md:L209] |

The default grade `'F'` is initialized at `let grade = 'F';` [Readme.md:L209] and is **retained whenever every `else if` test fails** (that is, when `percentage < 50`). The **upper bound** of each band (the `< 90`, `< 80`, `< 70`, `< 60`, `< 50`) is **implicit** in the ordered cascade rather than written as a literal comparison: a higher band is tested first, so — for example — `A` is reached only when `percentage >= 80` *and* the earlier `percentage >= 90` test has already failed [Readme.md:L211-L221]. These thresholds drive feature **F-005 (Grade Assignment)**; see [`../functionality/grading.md`](../functionality/grading.md) for the grade-decision flowchart.

---

## How It Works

All of the values above are **compile-time constants** embedded directly in the application's `script.js` and `index.html` source [Readme.md:L181-L221]. There is **no UI control, configuration file, environment variable, or runtime override** for the subject list, the per-subject maximum, the 500-point denominator, or the grade thresholds. Changing any one of them would require editing the source code itself — which is a code change and therefore **out of scope** for this documentation set. Because nothing is configurable at runtime, the schema is fully deterministic and identical on every run.

---

## Notes / Contract Pointer

These are the **authoritative fixed values** for the application — the single source of truth. Rather than restating them, related documents reference this page:

- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the **determinism guarantee** (every `percentage` maps to exactly one grade) and the **no-input-validation** invariant (marks are never clamped or range-checked).
- [`../functionality/grading.md`](../functionality/grading.md) — the consumer of the **grade thresholds**, including the grade-decision flowchart.
- [`../functionality/computation.md`](../functionality/computation.md) — the consumer of the **500-point denominator** and the percentage formula.

---

## Related Documents

- [Documentation Hub](../index.md) — back to the master table of contents.
- [Behavioral Contracts](../contracts/behavioral-contracts.md) — invariants, preconditions, postconditions, and the no-input-validation discussion.
- [Grading Functionality](../functionality/grading.md) — F-005 grade assignment and the grade-decision flowchart.
- [Computation Functionality](../functionality/computation.md) — F-003 total and F-004 percentage.
