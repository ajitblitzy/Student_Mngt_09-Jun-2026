# Getting Started

## Purpose

This guide gets a new user running the **Student Report Generator** with **zero installation**. The application is a browser-only static front-end built with HTML, CSS, and vanilla JavaScript, and its only external dependency is the jsPDF library, loaded from a CDN at page load [Readme.md:L53]. Running the app is as simple as opening `index.html` in a web browser — there is no build step, no server, and nothing to install [Readme.md:L277-L283].

Everything in this guide is **code-grounded**: every claim is traceable to the application source embedded in the repository-root `Readme.md` and is cited inline in the form `[Readme.md:Lx-Ly]`.

---

## Prerequisites

You need very little to run the Student Report Generator:

- **A modern web browser** — Chrome, Firefox, Edge, or Safari. The app is pure HTML/CSS/vanilla JavaScript, so any current browser can render and run it.
- **Internet access at page load — for PDF export only.** The page pulls the jsPDF library from the cdnjs CDN through a single `<script>` tag in the `<head>` [Readme.md:L53]:

  ```html
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  ```

  This script must resolve so that the `window.jspdf` global is available when you click **Download PDF** [Readme.md:L53] [Readme.md:L231]. **Only the PDF-export feature requires the CDN** — entering details, generating the on-screen report card, computing the total and percentage, and assigning the grade are all pure local JavaScript and work offline. If you have no internet access, the report card still renders; only **Download PDF** will fail. See [`../dependencies.md`](../dependencies.md) for the full CDN-availability contract.
- **No Node.js, no npm, no build step, no install.** This project has no `package.json` and no build system; "installing" simply means having the project files on disk. It is a genuine **zero-install** application.

---

## Run Steps

Follow these steps, adapted from the project's "How to Run" instructions [Readme.md:L277-L283]:

1. **Download or clone the project** so the repository files are on your machine [Readme.md:L279].
2. **Open `index.html` in a web browser** — double-click the file, or use your browser's **File → Open** dialog [Readme.md:L280].
3. **Enter the student details** — the student's name and roll number, plus the five subject marks: Maths, Science, English, History, and Computer [Readme.md:L281], using the form inputs at the top of the page [Readme.md:L61-L68].
4. **Click "Generate Report"** to compute the total, percentage, and grade and render the on-screen report card [Readme.md:L70] [Readme.md:L223-L227].
5. **Click "Download PDF"** to save the rendered report card as a PDF named `<name>_Report.pdf` [Readme.md:L71] [Readme.md:L251].

> **Note — embedded vs. standalone files.** The project tree advertises `index.html`, `style.css`, and `script.js` as standalone files [Readme.md:L29-L36], but in this repository the markup, styles, and logic currently live **embedded inside `Readme.md`**. This guide documents the intended run flow exactly as written (open `index.html` in a browser); extracting the embedded code into separate files is a code change and is out of scope for this documentation.

For the critical workflow-ordering rule — you must click **Generate Report** before **Download PDF** — and for troubleshooting, see [`usage.md`](usage.md).

---

## What Success Looks Like

When everything works, you will see two clear signals:

- **After clicking "Generate Report":** the on-screen **report card** populates with the student's name and roll number, a marks table containing one row per subject, the total marks, the percentage (shown to two decimal places), and a letter grade [Readme.md:L74-L93] [Readme.md:L223-L227].
- **After clicking "Download PDF":** your browser downloads a PDF file named `<name>_Report.pdf` — for example, `Asha_Report.pdf` for a student named *Asha* [Readme.md:L251].

| Action | Expected Result |
|---|---|
| Click **Generate Report** | The report card renders: name, roll number, a five-row marks table, total, percentage (two decimals), and grade [Readme.md:L74-L93] [Readme.md:L223-L227] |
| Click **Download PDF** | The browser downloads `<name>_Report.pdf` (e.g., `Asha_Report.pdf`) [Readme.md:L251] |

---

## Related Documents

- [`usage.md`](usage.md) — the usage walkthrough, the workflow-ordering rule (Generate Report before Download PDF), and troubleshooting.
- [`../dependencies.md`](../dependencies.md) — the jsPDF 2.5.1 CDN integration and the internet-access (CDN-availability) precondition.
- [`../index.md`](../index.md) — back to the Documentation Hub.
