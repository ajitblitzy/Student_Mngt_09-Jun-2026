# Dependencies — jsPDF (CDN Integration)

## Purpose

This page documents the Student Report Generator's **sole external runtime dependency**: the **jsPDF** library, version **2.5.1**, loaded from the **cdnjs (Cloudflare)** CDN [Readme.md:L38]. It is the precise reference for the `window.jspdf` **global contract** that the `downloadPDF()` function relies on to construct and save the report-card PDF [Readme.md:L215-L237]. The application has no package manifest and no build step; jsPDF is the only third-party code it pulls in, and it does so entirely at page load through a single `<script>` tag [Readme.md:L38]. All content here is **code-grounded** — every technical claim carries an inline `[Readme.md:Lx-Ly]` citation pointing at the application source embedded in the repository-root `Readme.md`. This is a documentation-only reference and does not modify any source code.

**Source:** `[Readme.md:L38]` (the CDN `<script>` tag) and `[Readme.md:L215-L237]` (the consuming `downloadPDF()` function).

---

## Dependency Inventory

The application depends on exactly **one** external library. There are no other third-party packages, no npm dependencies, and no locally vendored libraries — jsPDF is loaded straight from a CDN [Readme.md:L38]. It is also the single entry in the embedded README's "Technologies Used" list that is not a core web platform technology [Readme.md:L260].

| Dependency | Source (CDN) | Version | Build | Global Exposed | Consumed By |
|---|---|---|---|---|---|
| jsPDF | cdnjs (Cloudflare) | 2.5.1 | UMD minified (`jspdf.umd.min.js`) | `window.jspdf` | `downloadPDF()` [Readme.md:L215-L237] |

The dependency is declared by a single `<script>` tag in the `<head>` of `index.html`, pinned to the exact version `2.5.1` [Readme.md:L38]:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
```

The URL itself identifies every facet of the dependency: the **cdnjs (Cloudflare)** host, the **`jspdf`** package, the pinned **`2.5.1`** version, and the **UMD, minified** build artifact `jspdf.umd.min.js` [Readme.md:L38]. The UMD ("Universal Module Definition") build is the variant that publishes the library onto the browser's global object as `window.jspdf`.

---

## How It Works

The integration follows a simple **load → destructure → build → save** path. Each step below is taken directly from the source.

### Load order

The jsPDF `<script>` is placed in the document `<head>`, so the browser fetches and evaluates it **synchronously before the `<body>` is parsed** [Readme.md:L38]. By the time the user can interact with the two action buttons in the body — "Generate Report" and "Download PDF" [Readme.md:L55-L56] — and by the time the application's own `script.js` (loaded last, at the end of `<body>`) executes [Readme.md:L81], the `window.jspdf` global is already populated. This ordering is precisely what allows `downloadPDF()` to assume the library is present (see the contract below).

### Destructure → instantiate

Inside `downloadPDF()`, the code pulls the **`jsPDF`** constructor (capitalized) off the **`window.jspdf`** global (lowercase) by destructuring, then instantiates a document [Readme.md:L216-L218]:

```javascript
const { jsPDF } = window.jspdf;
const doc = new jsPDF();
```

> **Naming detail.** The UMD build exposes a global namespace object named **`window.jspdf`** (all lowercase); the document **constructor** is a named property of that namespace called **`jsPDF`** (capital `P`, capital `DF`) [Readme.md:L216]. Confusing the two is the most common jsPDF integration error, so the distinction is called out explicitly here.

### Build → save

The application uses only a **small, fixed subset** of the jsPDF API — it sets two font sizes, writes six lines of text, and saves the file [Readme.md:L226-L236]:

| jsPDF method | Usage in `downloadPDF()` | Source |
|---|---|---|
| `setFontSize(size)` | Sets the title size to `18`, then body text to `12` | [Readme.md:L226], [Readme.md:L229] |
| `text(string, x, y)` | Writes the heading and five report lines at `x = 20`, `y = 20/40/50/60/70/80` | [Readme.md:L227], [Readme.md:L230-L234] |
| `save(filename)` | Triggers the browser download of the generated PDF | [Readme.md:L236] |

No other jsPDF capabilities (tables, images, embedded fonts, multi-page handling, etc.) are used by the application, and they are therefore intentionally **not** documented here.

---

## Expected Behavior / Contract

This block states **the expectation from the code** — the conditions the jsPDF integration assumes, what it produces on success, and how it fails. The PDF-export feature's full DOM-read behavior is documented in [`functionality/pdf-export.md`](functionality/pdf-export.md) and [`contracts/behavioral-contracts.md`](contracts/behavioral-contracts.md); this page covers only the **dependency contract**.

| Aspect | Contract |
|---|---|
| **Precondition — CDN availability** | The cdnjs script `[Readme.md:L38]` MUST load successfully so that `window.jspdf` is defined **before** `downloadPDF()` runs. This is the **CDN-availability precondition**, and it requires **internet access at page load**; the application is otherwise zero-install and offline-capable. |
| **Precondition — ordering** | `downloadPDF()` reads from the **rendered report-card DOM**, not the form inputs, so **"Generate Report" must be clicked before "Download PDF"** [Readme.md:L220-L224]. The full **DOM-read invariant** lives in [`contracts/behavioral-contracts.md`](contracts/behavioral-contracts.md) and [`functionality/pdf-export.md`](functionality/pdf-export.md) and is not duplicated here. |
| **Postcondition — success** | A PDF file named `` `${name}_Report.pdf` `` is generated and downloaded [Readme.md:L236], where `name` is read from the rendered `#rName` element [Readme.md:L220]. |
| **Failure mode — CDN unreachable** | If the CDN is unreachable or blocked (offline, firewall, or CDN outage), `window.jspdf` is `undefined`, and the line `const { jsPDF } = window.jspdf;` [Readme.md:L216] throws a **`TypeError`** ("Cannot destructure property 'jsPDF' of 'undefined' …"). There is **no `try`/`catch` and no programmatic error handling** anywhere in the code, so the failure surfaces **only in the browser console** — the page displays no on-screen error. This is the documented, expected behavior. |
| **Version contract** | The application is pinned to jsPDF **2.5.1** [Readme.md:L38]. This documentation records the in-use version **as-is** and does **not** upgrade it; any version change would be a code task and is out of scope. This pinned version has **known published security advisories** — see [Security Considerations](#security-considerations) below. |

> **Summary.** Given internet access at load (so `window.jspdf` is defined) and a report generated first (so the rendered DOM is populated), `downloadPDF()` deterministically produces a download named `<name>_Report.pdf` [Readme.md:L236]. Remove either precondition and the function either throws a `TypeError` (no jsPDF available) or exports a report card built from empty/stale DOM values (no prior "Generate Report").

---

## Security Considerations

The pinned runtime dependency is **jsPDF 2.5.1** [Readme.md:L38]. This documentation-only task records that version **as-is** and does **not** change it — changing the pinned CDN version is a code/dependency task and is **out of scope** per AAP §0.8.2. For an accurate security posture, the known risk of the pinned version is surfaced here rather than silently omitted.

### Known advisories for jsPDF 2.5.1

Public vulnerability databases (OSV, Snyk, and GitHub Security Advisories) track **multiple known advisories** against jsPDF 2.5.1. A read-only OSV query for the package coordinate `pkg:npm/jspdf@2.5.1` surfaces advisories spanning the following categories:

| Category | Summary | Affected API surface |
|---|---|---|
| Denial of service (ReDoS) | An inefficient regular expression in `setDisplayMode` can be driven to high CPU load (regular-expression denial of service). | `setDisplayMode` |
| Denial of service (CPU exhaustion) | User-controlled input to `addImage` can cause excessive CPU utilization. | `addImage` |
| Local file inclusion / path traversal | A later disclosure (CVE-2025-68428 / GHSA-f8cm-6447-x5h2) allows reading arbitrary files via a user-controlled `addImage` path and embedding their contents in the output PDF. **This affects the Node.js builds only** (`dist/jspdf.node.js`), **not** the browser UMD build used by this project. | `addImage` (Node.js builds) |
| Injection-class / metadata | Additional object- and metadata-injection-class advisories are reported against the 2.x line by the security databases. | various |

> The exact advisory inventory and severities evolve over time; consult OSV, Snyk, or GitHub Security Advisories for the current authoritative list. The categories above are representative, not exhaustive.

### Reachability in this application

This project loads the **browser UMD build** from cdnjs [Readme.md:L38], and `downloadPDF()` uses only **`setFontSize`**, **`text`**, and **`save`** [Readme.md:L215-L237] — it never calls `addImage` or `setDisplayMode`. Consequently:

- The `addImage`-based advisories (the CPU-exhaustion DoS and the CVE-2025-68428 path-traversal/LFI) are **not exercised** by the current code path; the path-traversal CVE additionally affects only the Node.js builds, not the browser UMD build in use here.
- The `setDisplayMode` ReDoS is likewise **not reached**.

Reachability **reduces**, but does not eliminate, the dependency risk: the vulnerable code still ships inside the loaded library, and any future code change that begins calling the affected methods would expose the application. The risk is therefore documented rather than dismissed.

### Follow-up work item

- **Evaluate upgrading jsPDF** to a current patched release in a **separate code/dependency task**, and re-verify the `downloadPDF()` integration (`setFontSize`/`text`/`save`) after any upgrade. This evaluation is **out of scope** for the present documentation-only change (AAP §0.8.2) and must not alter the pinned CDN version in this task.

---

## Related Documents

- [Documentation Hub](index.md) — back to the master table of contents.
- [`api-reference/script-js.md`](api-reference/script-js.md) — the `generateReport()` and `downloadPDF()` function reference (exact signatures, DOM reads/writes, side effects).
- [`functionality/pdf-export.md`](functionality/pdf-export.md) — the **F-007 PDF export** feature guide, including the DOM-read invariant and the PDF layout spec.
- [`reference/data-schema.md`](reference/data-schema.md) — the single source of truth for the academic fixed values (five subjects, per-subject maximum, 500-point total, grade thresholds); the runtime/dependency behavior lives in [`contracts/behavioral-contracts.md`](contracts/behavioral-contracts.md) below.
- [`contracts/behavioral-contracts.md`](contracts/behavioral-contracts.md) — invariants, preconditions, postconditions, and error modes (the single source of truth for the DOM-read invariant and the jsPDF-present precondition).
- [`guides/getting-started.md`](guides/getting-started.md) — the zero-install run guide; notes the internet-access prerequisite for the CDN.
