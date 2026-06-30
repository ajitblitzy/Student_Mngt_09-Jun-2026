# Architecture Overview

## Purpose

This page gives the **high-level system context** for the **Student Report Generator** — what the application is, the architectural posture it adopts, the capabilities it delivers, and the criteria by which it is judged correct. It is the architectural entry point that the deeper [`component-model.md`](component-model.md) and [`data-flow.md`](data-flow.md) documents drill into. All content on this page is **code-grounded**: every technical claim carries an inline `[Readme.md:Lx-Ly]` citation pointing at the application source embedded in the repository-root `Readme.md`. This is a documentation-only reference and does not modify any source code.

**Source Location:** `[Readme.md:L1-L21]` — the project Overview and structure; the full embedded application source (markup, styling, and logic) resides at `[Readme.md:L27-L238]`.

---

## System Context

The Student Report Generator is a small **educational/utility tool**. It lets a user enter student details, calculate total marks and percentage, generate a formatted on-screen report, and export that report as a PDF file `[Readme.md:L4-L8]`.

Architecturally, the application is deliberately minimal. It has **no backend, no persistence, and no build pipeline**; it is **zero-install** and runs entirely client-side — a user simply opens `index.html` in a browser, enters details, and clicks the action buttons `[Readme.md:L255-L268]`. The technology stack is plain HTML, CSS, and JavaScript, plus the jsPDF library `[Readme.md:L255-L268]`.

The application's **only external dependency** is **jsPDF 2.5.1**, loaded as a UMD script from the **cdnjs** (Cloudflare) CDN `[Readme.md:L38]`. Because that library is fetched over the network when the page loads, the PDF-export capability requires **internet access** at load time. The integration contract and the CDN-availability precondition are documented in [`../dependencies.md`](../dependencies.md) and are not duplicated here.

---

## Capability Summary

The four headline capabilities, taken verbatim from the project Overview `[Readme.md:L4-L8]`, are:

- **Enter student details using a form** `[Readme.md:L4-L8]`
- **Calculate total marks and percentage** `[Readme.md:L4-L8]`
- **Generate a formatted student report** `[Readme.md:L4-L8]`
- **Export the report as a PDF file** `[Readme.md:L4-L8]`

Each capability maps to one or more features in the application's canonical feature decomposition (F-001 through F-008). The table below is intentionally brief; the detailed feature documentation lives under [`../functionality/`](../functionality/), and the fixed values these capabilities depend on — the five subjects, the per-subject maximum, the `500`-point denominator, and the grade thresholds — are owned by [`../reference/data-schema.md`](../reference/data-schema.md), the single source of truth for fixed values.

| Capability `[Readme.md:L4-L8]` | Feature ID(s) | Detailed Documentation |
|---|---|---|
| Enter student details using a form | F-001 Identity Capture, F-002 Marks Entry | [`../functionality/data-entry.md`](../functionality/data-entry.md) |
| Calculate total marks and percentage | F-003 Total, F-004 Percentage | [`../functionality/computation.md`](../functionality/computation.md) |
| Generate a formatted student report | F-005 Grade, F-006 On-Screen Render, F-008 Styling | [`../functionality/grading.md`](../functionality/grading.md), [`../functionality/report-rendering.md`](../functionality/report-rendering.md), [`../functionality/styling.md`](../functionality/styling.md) |
| Export the report as a PDF file | F-007 PDF Export | [`../functionality/pdf-export.md`](../functionality/pdf-export.md) |

---

## Success Criteria

The system is working correctly when two outputs are produced:

1. A **correct on-screen report** — the student name, roll number, a per-subject marks table, the total, the percentage rendered to **two decimal places**, and the assigned grade — written into the rendered report-card DOM `[Readme.md:L177-L212]`.
2. A **downloadable PDF that matches the rendered values**, saved as `<name>_Report.pdf` `[Readme.md:L215-L237]`.

By construction, the PDF mirrors the on-screen report: `downloadPDF()` builds the document by reading the **rendered DOM**, not the original **form inputs** `[Readme.md:L220-L224]`. This **DOM-read invariant** — and the ordering precondition it implies (Generate Report must run before Download PDF) — is defined authoritatively in [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) and traced step-by-step in [`data-flow.md`](data-flow.md); it is referenced here, not duplicated.

---

## High-Level Architecture

The diagram below shows the end-to-end runtime architecture: a **User** interacts with the **browser runtime** (the `index.html` form inputs and report-card DOM, the `script.js` logic, and the `style.css` presentation), which loads **jsPDF 2.5.1** from **cdnjs** and ultimately produces a downloadable **PDF artifact**.

```mermaid
flowchart LR
    User([User])
    subgraph Browser["Browser Runtime (zero-install, client-side)"]
        direction TB
        HTML["index.html<br/>form inputs + report-card DOM"]
        JS["script.js<br/>generateReport / downloadPDF"]
        CSS["style.css<br/>layout, palette, tables"]
    end
    CDN["cdnjs<br/>jsPDF 2.5.1 UMD"]
    PDF["PDF artifact<br/>name_Report.pdf"]
    User -->|"enter details, click buttons"| HTML
    HTML -->|"loads stylesheet (L35)"| CSS
    HTML -->|"loads logic (L81)"| JS
    HTML -->|"loads library (L38)"| CDN
    CDN -.->|"window.jspdf (L216)"| JS
    JS -->|"doc.save (L236)"| PDF
    PDF -->|"downloaded"| User
```

*High-level architecture, validated against `[Readme.md:L35]`, `[Readme.md:L38]`, `[Readme.md:L81]`, `[Readme.md:L216]`, `[Readme.md:L236]`.*

---

## Related Documents

This page is the hub of the architecture set; each document below links back to it and to the Documentation Hub (paths relative to `docs/architecture/`):

- [`component-model.md`](component-model.md) — the three logical components (markup, styling, logic) plus the jsPDF dependency, with their responsibilities and a component-relationship diagram.
- [`data-flow.md`](data-flow.md) — the DOM-mediated end-to-end data flow and the per-function flowcharts for `generateReport()` and `downloadPDF()`.
- [`../dependencies.md`](../dependencies.md) — the jsPDF 2.5.1 CDN integration contract and the CDN-availability precondition.
- [`../reference/data-schema.md`](../reference/data-schema.md) — the single source of truth for the fixed values behind these capabilities: the five subjects, the `500`-point denominator, and the grade thresholds.
- [`../index.md`](../index.md) — back to the Documentation Hub.

---

*Maintenance note: this page is derived from the application source embedded in `Readme.md` `[Readme.md:L1-L238]`. If that embedded source changes, update the cited line ranges, the prose, and the architecture diagram on this page so this overview stays accurate.*
