# CSS Reference

## Purpose

This page is the selector and style-declaration reference for the Student Report Generator's presentation layer — feature **F-008, static visual styling**. It catalogs every rule in the application's stylesheet so that each visual decision (layout, color palette, typography, and table borders) is traceable to the exact source line it comes from. It is the precise companion to the prose feature guide at [`../functionality/styling.md`](../functionality/styling.md): that guide explains *why* the styling looks the way it does, while this reference documents *what* each rule declares.

All content on this page is code-grounded — it is extracted from the `style.css` block embedded in the repository-root `Readme.md`, and every technical claim carries an inline `Readme.md` line-range citation.

## Source Location

- **Stylesheet (`style.css`):** [Readme.md:L91-L155] — the embedded `style.css` fenced code block, which contains exactly eleven rule-blocks.
- **Stylesheet link (markup):** the page loads the stylesheet via `<link rel="stylesheet" href="style.css">` [Readme.md:L35].
- **Viewport meta (only responsiveness directive):** the markup `<head>` declares `<meta name="viewport" ...>` [Readme.md:L32].

---

## How It Works

The presentation layer is **purely declarative CSS** — the eleven rule-blocks in `style.css` paint the page and contribute **no behavior** [Readme.md:L91-L155]. The browser applies them once the stylesheet is linked from the markup [Readme.md:L35]; the JavaScript never adds or removes classes or inline styles, so the styling is **static at runtime** — the only runtime DOM mutation is *content* (via `.innerHTML` / `.innerText`), never *style* [Readme.md:L161-L238]. The rules group into three concerns: **layout** (`body`, `.container`, `.form-section`, `h1, h2`, `input` [Readme.md:L92-L121]), the **color palette** (blue action buttons over light-grey surfaces [Readme.md:L123-L134]), and **table styling** (`.report-card`, `table`, `table, th, td`, `th, td` [Readme.md:L136-L154]). The selector-by-selector catalog below documents *what* each of the eleven rules declares.

---

## Selector / Style Reference

The stylesheet contains **exactly eleven** rule-blocks, listed below in source order. Each row gives the selector, its key declarations, and the visual purpose it serves, with a citation to the precise line range inside the embedded `style.css` [Readme.md:L91-L155].

| Selector | Key Declarations | Purpose |
|---|---|---|
| `body` | `font-family: Arial, sans-serif; background: #f4f6f8; margin: 0; padding: 20px` | Base typography, light-grey page background, and a 20px page gutter [Readme.md:L92-L97] |
| `.container` | `max-width: 800px; margin: auto; background: white; padding: 30px; border-radius: 10px; box-shadow: 0 0 10px rgba(0,0,0,0.1)` | Centered white "card" that holds the whole app — capped at 800px wide, with rounded corners and a soft drop shadow [Readme.md:L99-L106] |
| `h1, h2` | `text-align: center` | Centers the app title (`h1`) and the report heading (`h2`) [Readme.md:L108-L110] |
| `.form-section` | `display: grid; gap: 10px; margin-bottom: 30px` | Vertical CSS-grid layout for the input form, with 10px gaps between fields and a 30px gap below [Readme.md:L112-L116] |
| `input` | `padding: 10px; font-size: 16px` | Comfortable, legible form fields [Readme.md:L118-L121] |
| `button` | `padding: 12px; background: #007bff; color: white; border: none; cursor: pointer; border-radius: 5px` | Primary blue action buttons (Generate Report / Download PDF) [Readme.md:L123-L130] |
| `button:hover` | `background: #0056b3` | Darker-blue hover state for the action buttons [Readme.md:L132-L134] |
| `.report-card` | `border-top: 2px solid #ddd; padding-top: 20px` | Visually separates the rendered report from the input form via a top border [Readme.md:L136-L139] |
| `table` | `width: 100%; border-collapse: collapse; margin-top: 15px` | Full-width marks table with collapsed (single) borders [Readme.md:L141-L145] |
| `table, th, td` | `border: 1px solid #ccc` | Light-grey grid borders on the table, header cells, and data cells [Readme.md:L147-L149] |
| `th, td` | `padding: 10px; text-align: center` | Padded, center-aligned table header and data cells [Readme.md:L151-L154] |

> **Note — single (non-doubled) cell borders:** `border-collapse: collapse` on `table` [Readme.md:L143] combined with `table, th, td { border: 1px solid #ccc; }` [Readme.md:L147-L149] merges adjacent cell edges into a single 1px line instead of the default doubled borders.

> **Note — grouped selectors are distinct rules:** `table, th, td` [Readme.md:L147-L149] and `th, td` [Readme.md:L151-L154] are two separate rule-blocks; both are counted among the eleven.

### Color palette

The stylesheet uses three literal colors for its surfaces and actions (the table-border and report-card greys are documented in the reference table above):

| Color | Hex | Used by | Source |
|---|---|---|---|
| Page background | `#f4f6f8` | `body` | [Readme.md:L94] |
| Action button | `#007bff` | `button` | [Readme.md:L125] |
| Button hover | `#0056b3` | `button:hover` | [Readme.md:L133] |

---

## Responsiveness

The application's **only** responsiveness mechanism is the viewport `<meta>` tag declared in the markup [Readme.md:L32]:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

There are **no `@media` query rules anywhere** in `style.css` [Readme.md:L91-L155]. The layout's fluid behavior comes entirely from the `.container` rule — `max-width: 800px; margin: auto;` [Readme.md:L99-L106] — which lets the card shrink on narrow viewports while staying centered and capped at 800px on wide ones, working together with the grid-based `.form-section` [Readme.md:L112-L116] and the `width: 100%` marks `table` [Readme.md:L141-L145].

The README markets the project as having a **"Responsive UI"** [Readme.md:L253]. Stated accurately, that responsiveness is achieved through the viewport meta tag [Readme.md:L32] plus the fluid `max-width: 800px` container [Readme.md:L100] — **not** through media-query breakpoints, of which there are none. See [`../functionality/styling.md`](../functionality/styling.md) for the feature-level discussion of F-008.

---

## Expected Behavior / Contract

This `css-reference` describes a **style contract** rather than a behavioral one: it specifies what the stylesheet is expected to present, not runtime logic. The contract below captures the precondition (input), the expected presentation (output), side effects, invariants, and failure behavior.

| Aspect | Expectation |
|---|---|
| **Precondition (input)** | The stylesheet is linked from the markup via `<link rel="stylesheet" href="style.css">` [Readme.md:L35] and loads successfully; the eleven rule-blocks [Readme.md:L91-L155] then apply to the page. |
| **Expected presentation (output)** | With the stylesheet applied, the app renders as a centered white card (≤ 800px wide) on a light-grey (`#f4f6f8`) background, presenting a vertically stacked form of padded, 16px inputs, blue (`#007bff`) action buttons that darken to `#0056b3` on hover, and — within the rendered DOM report card — a full-width, center-aligned marks table with single 1px `#ccc` borders beneath a top-bordered (`#ddd`) report-card section [Readme.md:L92-L154]. |
| **Side effects** | None. CSS is purely declarative; it paints the DOM and never reads, computes, or persists state, and it runs no JavaScript. |
| **Invariants** | Presentation is **static and deterministic** — the same markup always renders the same way. The JavaScript never adds or removes classes or inline styles, so styling does not change at runtime; the only runtime DOM mutation is *content* (via `.innerHTML` / `.innerText`), never *style* [Readme.md:L161-L238]. Responsiveness is provided solely by the viewport `<meta>` tag [Readme.md:L32] plus the fluid `max-width: 800px` container [Readme.md:L100]; there are **no `@media` breakpoints** [Readme.md:L91-L155]. |
| **Error modes / failure behavior** | If the stylesheet is absent or fails to load, the page falls back to unstyled browser defaults but remains **fully functional** — data entry, computation, grading, rendering, and PDF export are independent of styling and run identically with or without it. |
| **Presentation independence** | This styling has **no effect** on the computation or PDF-export logic, which are documented separately. |

---

## Related Documents

- [`../functionality/styling.md`](../functionality/styling.md) — the **F-008 static visual styling** feature guide; the prose companion to this reference.
- [`html-structure.md`](html-structure.md) — the DOM element/ID reference for the markup these selectors target.
- [`../index.md`](../index.md) — back to the Documentation Hub.
