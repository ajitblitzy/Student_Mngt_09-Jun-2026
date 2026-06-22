# Dependencies — jsPDF (CDN Integration)

## Purpose

This page documents the application's **sole external runtime dependency**: the **jsPDF**
library, version **2.5.1**, loaded from the **cdnjs (Cloudflare)** CDN. It records the
**CDN-availability precondition** and the **`window.jspdf` global contract** that the
`downloadPDF()` function relies on to produce a PDF. jsPDF is the only third-party library the
project uses; everything else is plain HTML, CSS, and vanilla JavaScript, so there is no package
manager, no build step, and nothing to `npm install` — the library is delivered straight to the
browser by a single `<script>` tag.

All content on this page is code-grounded: it is extracted from the application source embedded
in the repository-root `Readme.md`, and every technical claim carries an inline
`Readme.md` line-range citation.

## Source Location

- **CDN `<script>` tag:** [Readme.md:L53]
- **Consumer (`downloadPDF()`):** [Readme.md:L230-L252]

---

## Dependency Inventory

The application declares **exactly one** external dependency. It is listed in the embedded
README's "Technologies Used" section [Readme.md:L275] and pulled in at runtime by the CDN
`<script>` tag in the page `<head>` [Readme.md:L53].

| Dependency | Source (CDN) | Version | Build | Global Exposed | Consumed By |
|---|---|---|---|---|---|
| jsPDF | cdnjs (Cloudflare) | 2.5.1 | UMD minified (`jspdf.umd.min.js`) | `window.jspdf` | `downloadPDF()` [Readme.md:L230-L252] |

The exact CDN URL, taken verbatim from the markup [Readme.md:L53]:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
```

The filename `jspdf.umd.min.js` identifies the build flavor: a **UMD** (Universal Module
Definition) **minified** bundle. Because it is the UMD build loaded via a plain `<script>` tag
(rather than an ES-module import), jsPDF attaches itself to a **global** object on `window` —
specifically `window.jspdf` (all lowercase) [Readme.md:L231].

---

## How It Works

The dependency moves through four stages — **load → destructure → build → save** — entirely on
the client, with no server involvement.

**1. Load (in `<head>`, synchronously).** The CDN `<script>` is placed in the document `<head>`
[Readme.md:L53], while the application's own logic, `script.js`, is loaded last, at the very end
of `<body>` [Readme.md:L96]. Because the head script is a plain synchronous `<script>`, the
browser fetches and evaluates it before it parses the body, so by the time the user can click
either action button — "Generate Report" or "Download PDF" [Readme.md:L70-L71] — the
`window.jspdf` global has already been populated (provided the CDN was reachable).

**2. Destructure & build.** Inside `downloadPDF()`, the capitalized **`jsPDF` constructor** is
pulled off the lowercase **`window.jspdf`** global by destructuring, then instantiated
[Readme.md:L231-L233]:

```javascript
const { jsPDF } = window.jspdf;
const doc = new jsPDF();
```

Note the deliberate casing distinction: **`window.jspdf`** (lowercase) is the global namespace
the UMD bundle installs, and **`jsPDF`** (capitalized) is the constructor exposed as a property
of that namespace [Readme.md:L231].

**3. API surface actually used.** `downloadPDF()` uses only three jsPDF methods on the `doc`
instance [Readme.md:L241-L251]. No other jsPDF capability is touched by this application:

| Method | Usage in `downloadPDF()` | Source |
|---|---|---|
| `setFontSize(size)` | Sets the title font to `18`, then the body font to `12` | [Readme.md:L241], [Readme.md:L244] |
| `text(string, x, y)` | Writes each line at `x = 20`, with `y` at `20 / 40 / 50 / 60 / 70 / 80` | [Readme.md:L242], [Readme.md:L245-L249] |
| `save(filename)` | Triggers the browser download of the generated PDF | [Readme.md:L251] |

**4. Save.** The document is written to disk by the browser with a templated filename
[Readme.md:L251]:

```javascript
doc.save(`${name}_Report.pdf`);
```

---

## Expected Behavior / Contract

This section states the **expectation from the code** for the jsPDF integration — its
preconditions, postconditions, and error modes. The full DOM-read invariant and the broader
behavioral guarantees are not duplicated here; they live in
[`contracts/behavioral-contracts.md`](contracts/behavioral-contracts.md) and the feature guide
[`functionality/pdf-export.md`](functionality/pdf-export.md), which this page cross-links to.

| Aspect | Expectation | Source |
|---|---|---|
| Precondition — CDN availability | The cdnjs `<script>` must load successfully so that `window.jspdf` is defined **before** `downloadPDF()` runs. This requires **internet access at page load**. | [Readme.md:L53], [Readme.md:L231] |
| Precondition — workflow ordering | "Generate Report" must be clicked **before** "Download PDF", because `downloadPDF()` reads the **rendered DOM**, not the form inputs (the **DOM-read invariant**). | [Readme.md:L70-L71], [Readme.md:L235] |
| Postcondition — success | A PDF named `` `${name}_Report.pdf` `` is generated and downloaded, where `name` is read from the rendered `#rName` element. | [Readme.md:L235], [Readme.md:L251] |
| Error mode — missing dependency | If `window.jspdf` is `undefined`, the destructuring line throws a `TypeError`; there is **no programmatic handling**. | [Readme.md:L231] |
| Version contract | The app is pinned to jsPDF **2.5.1**; this is documented **as-is** and is not upgraded. | [Readme.md:L53] |

The points below elaborate the contract:

- **CDN-availability precondition.** jsPDF arrives only over the network from cdnjs
  [Readme.md:L53]. The rest of the application is **zero-install / offline-capable** — it is
  just static HTML, CSS, and JavaScript — but this single CDN fetch is the one moment that
  requires connectivity. If the page is first loaded with internet access, `window.jspdf` is
  populated and PDF export works for the rest of the session.

- **Ordering precondition (DOM-read invariant).** `downloadPDF()` reads the **rendered
  report-card DOM** (for example, the student name from `#rName`) rather than the raw form
  fields [Readme.md:L235]. The report card is only populated after "Generate Report" runs, so
  the user **must Generate before Download**. The complete invariant is documented in
  [`contracts/behavioral-contracts.md`](contracts/behavioral-contracts.md) and
  [`functionality/pdf-export.md`](functionality/pdf-export.md); this page intentionally does not
  restate it.

- **Failure mode — `TypeError`, surfaced only in the console.** If the CDN is unreachable or
  blocked (the user is offline, a firewall or content blocker strips the request, or cdnjs has
  an outage), the `<script>` at [Readme.md:L53] never defines `window.jspdf`, leaving it
  `undefined`. The first line of `downloadPDF()` then attempts to destructure that `undefined`
  value [Readme.md:L231]:

  ```javascript
  const { jsPDF } = window.jspdf;
  ```

  This throws a **`TypeError`** along the lines of *"Cannot destructure property 'jsPDF' of
  'undefined' as it is undefined."* The code contains **no `try`/`catch` and no programmatic
  error handling** for this case, so the failure surfaces **only in the browser's developer
  console** — there is no on-screen message, alert, or fallback. This is the documented,
  expected behavior, not a bug to be patched within the scope of this documentation effort.

- **Success postcondition.** When the precondition holds, `doc.save()` writes the file and the
  browser downloads it as `` `${name}_Report.pdf` `` [Readme.md:L251] — for example, a student
  named `Asha` yields `Asha_Report.pdf`.

- **Version contract.** The integration is pinned to **jsPDF 2.5.1** via the exact CDN URL
  [Readme.md:L53]. This documentation records that in-use version **as-is** and does **not**
  recommend or perform an upgrade; changing the version would be a code change and is out of
  scope.

---

## Related Documents

- [Documentation Hub](index.md) — back to the master table of contents.
- [`api-reference/script-js.md`](api-reference/script-js.md) — function reference for `downloadPDF()` and `generateReport()`.
- [`contracts/behavioral-contracts.md`](contracts/behavioral-contracts.md) — the full set of invariants, preconditions, and the `TypeError` error mode.
- [`functionality/pdf-export.md`](functionality/pdf-export.md) — the F-007 PDF-export feature guide, including the DOM-read invariant and layout spec.
- [`guides/getting-started.md`](guides/getting-started.md) — zero-install run guide (prerequisites include internet access for the CDN).
