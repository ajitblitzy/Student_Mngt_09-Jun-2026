# Getting Started

## Purpose

This guide gets a new user running the **Student Report Generator** with **zero installation**.
The application is a browser-only static front-end — plain HTML, CSS, and vanilla JavaScript — so
there is nothing to install, build, or deploy: you simply open a page in a web browser and use it.
All content on this page is code-grounded in the repository-root `Readme.md`, with an inline
`Readme.md` line-range citation on every code-derived claim.

For the day-to-day usage walkthrough, the critical workflow-ordering rule, and troubleshooting, see
[`usage.md`](usage.md). For the jsPDF CDN dependency details, see
[`../dependencies.md`](../dependencies.md).

---

## Prerequisites

The Student Report Generator is intentionally **zero-install**. You need only the following:

- **A modern web browser** — Chrome, Firefox, Edge, or Safari. The application is pure HTML, CSS,
  and vanilla JavaScript, so any current browser runs it without plugins or extensions.
- **Internet access at page load — for PDF export only.** The PDF-export feature relies on the
  **jsPDF** library, which is loaded from a CDN (cdnjs) by a `<script>` tag in the page `<head>`
  [Readme.md:L38]. That script must resolve when the page first loads so the `window.jspdf` global
  is available to the **Download PDF** feature [Readme.md:L216]. Entering data and clicking
  **Generate Report** — the on-screen report card — is pure local JavaScript and works **offline**;
  only **Download PDF** needs the CDN. See [`../dependencies.md`](../dependencies.md) for the full
  CDN-availability contract.
- **No Node.js, no npm, no build step, no install.** This project has no `package.json` and no
  build system. There is nothing to compile and no server to start — opening the page in a browser
  is the entire setup.

The single line that introduces the one external dependency, taken verbatim from the markup
[Readme.md:L38]:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
```

---

## Run Steps

Follow these steps to produce a student report. They expand the five-step "How to Run" list from
the embedded README [Readme.md:L262-L268]:

1. **Download or clone the project.** Get the repository onto your machine so the application files
   are available locally.
2. **Open `index.html` in a web browser.** Double-click the file, or use your browser's
   **File → Open** menu [Readme.md:L265].
3. **Enter the student details.** Fill in the student name, the roll number, and the five subject
   marks — Maths, Science, English, History, and Computer — using the form inputs. For more on
   these inputs, see [`../functionality/data-entry.md`](../functionality/data-entry.md).
4. **Click "Generate Report".** This computes the total, percentage, and grade and renders the
   on-screen **report card** [Readme.md:L55, L208-L212].
5. **Click "Download PDF".** This saves the report as `<name>_Report.pdf` [Readme.md:L56, L236].

**Note on embedded source files.** The project tree advertises `index.html`, `style.css`, and
`script.js` as standalone files [Readme.md:L14-L21], but in this repository the markup, styles, and
logic currently live **embedded inside `Readme.md`** rather than as physical standalone files. This
guide documents the **intended run flow as written** (open `index.html` in a browser) and does not
cover extracting the embedded code into separate files, which is out of scope for this
documentation.

For the critical workflow-ordering rule — **Generate Report** must run **before** **Download PDF** —
and for troubleshooting, see [`usage.md`](usage.md).

---

## What Success Looks Like

When the app is working correctly, you will observe the following:

- **After Generate Report**, the on-screen **report card** populates with the student name, the roll
  number, a marks table with one row per subject, the total marks, the percentage (shown to two
  decimals), and a letter grade [Readme.md:L59-L78, L208-L212].
- **After Download PDF**, the browser downloads a PDF file named `<name>_Report.pdf` — for example, a
  student named `Asha` produces `Asha_Report.pdf` [Readme.md:L236].

At a glance:

| Action | Expected Result | Source |
|---|---|---|
| Click **Generate Report** | The report card renders the name, roll number, per-subject marks table, total, percentage (two decimals), and grade. | [Readme.md:L59-L78, L208-L212] |
| Click **Download PDF** | The browser saves the report as `<name>_Report.pdf` (e.g., `Asha_Report.pdf`). | [Readme.md:L236] |

---

## Related Documents

- [`usage.md`](usage.md) — the usage walkthrough, workflow ordering (Generate before Download), and
  troubleshooting.
- [`../dependencies.md`](../dependencies.md) — the jsPDF 2.5.1 CDN integration and the
  internet-access (CDN-availability) precondition.
- [`../index.md`](../index.md) — back to the Documentation Hub.
