# CSS Reference

## Purpose

This page is the **selector / style reference** for the Student Report Generator's presentation layer — feature **F-008 static visual styling**. It catalogs every CSS selector and its key declarations, and it serves as the precise style companion to the prose feature guide in [`../functionality/styling.md`](../functionality/styling.md). All content here is **code-grounded**: it is extracted from the `style.css` block embedded in the repository-root `Readme.md`, and every technical claim carries an inline `[Readme.md:Lx-Ly]` citation. This is a documentation-only reference and does not modify any source code.

**Source:** `[Readme.md:L91-L155]`

---

## Selector / Style Reference

The stylesheet contains **exactly eleven rule-blocks**, listed below in source order `[Readme.md:L91-L155]`. The grouped selectors `table, th, td` and `th, td` are two distinct rules and both appear. The stylesheet is entirely static and purely presentational — it declares **no `@media` queries, flexbox, animations, or transitions** `[Readme.md:L91-L155]`.

| Selector | Key Declarations | Purpose |
|---|---|---|
| `body` | `font-family: Arial, sans-serif; background: #f4f6f8; margin: 0; padding: 20px` | Base typography (Arial), a light-grey page background, zero default margin, and a 20px page gutter `[Readme.md:L92-L97]` |
| `.container` | `max-width: 800px; margin: auto; background: white; padding: 30px; border-radius: 10px; box-shadow: 0 0 10px rgba(0,0,0,0.1)` | Centered white "card" that holds the whole app, capped at 800px wide, with rounded corners and a soft drop shadow `[Readme.md:L99-L106]` |
| `h1, h2` | `text-align: center` | Center the app title (`<h1>`) and the report heading (`<h2>`) `[Readme.md:L108-L110]` |
| `.form-section` | `display: grid; gap: 10px; margin-bottom: 30px` | Single-column grid layout for the input form, with 10px gaps between fields and 30px spacing below `[Readme.md:L112-L116]` |
| `input` | `padding: 10px; font-size: 16px` | Comfortable padding and a legible 16px font for every form field `[Readme.md:L118-L121]` |
| `button` | `padding: 12px; background: #007bff; color: white; border: none; cursor: pointer; border-radius: 5px` | Primary blue action buttons with white text, no border, a pointer cursor, and rounded corners `[Readme.md:L123-L130]` |
| `button:hover` | `background: #0056b3` | Darker-blue hover state for the action buttons `[Readme.md:L132-L134]` |
| `.report-card` | `border-top: 2px solid #ddd; padding-top: 20px` | A 2px light-grey top border with 20px top padding that visually separates the rendered report from the form `[Readme.md:L136-L139]` |
| `table` | `width: 100%; border-collapse: collapse; margin-top: 15px` | Full-width marks table with collapsed borders and 15px of top margin `[Readme.md:L141-L145]` |
| `table, th, td` | `border: 1px solid #ccc` | A 1px light-grey border on the table itself and on every header and data cell `[Readme.md:L147-L149]` |
| `th, td` | `padding: 10px; text-align: center` | Padded, center-aligned table header and data cells `[Readme.md:L151-L154]` |

### Color palette

The entire palette is three colors — the page background and the two button states (`#f4f6f8` at `[Readme.md:L94]`, `#007bff` at `[Readme.md:L125]`, `#0056b3` at `[Readme.md:L133]`):

```css
background: #f4f6f8; /* page background       (L94)  */
background: #007bff; /* button (primary blue) (L125) */
background: #0056b3; /* button:hover (darker) (L133) */
```

> **Table-border detail.** `border-collapse: collapse` `[Readme.md:L143]` combined with `table, th, td { border: 1px solid #ccc; }` `[Readme.md:L147-L149]` yields single, non-doubled `#ccc` cell borders instead of the browser-default doubled borders between adjacent cells.

---

## Responsiveness

The application's **only responsiveness mechanism is the viewport `<meta>` tag** `[Readme.md:L32]`:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

There are **no `@media` query rules anywhere** in `style.css` `[Readme.md:L91-L155]`. The fluid behavior that does exist comes from two ordinary static rules rather than from breakpoints:

- `.container { max-width: 800px; margin: auto; }` lets the card grow fluidly up to an 800px cap while staying centered at any viewport width `[Readme.md:L99-L106]`.
- `.form-section { display: grid; gap: 10px; }` makes the form a single-column grid that naturally reflows to the available width `[Readme.md:L112-L116]`.

The embedded README advertises a "Responsive UI" feature `[Readme.md:L253]`. To state this accurately: that responsiveness is delivered by the viewport meta tag plus the fluid `max-width: 800px` container `[Readme.md:L100]` and the grid form — **not** by CSS media queries, of which there are none. See [`../functionality/styling.md`](../functionality/styling.md) for the feature-level discussion of **F-008 static visual styling**.

---

## Expected Presentation

With this stylesheet applied, the app renders as a **centered white card (≤ 800px wide) on a light-grey background**: a vertically stacked form of padded, 16px-font inputs; blue action buttons that darken to `#0056b3` on hover; and, beneath a top-bordered report-card section, a full-width, center-aligned marks table with single 1px `#ccc` grid borders `[Readme.md:L92-L154]`. This styling is **static and purely presentational** — it governs only the appearance of the rendered DOM and has no effect on the computation or PDF-export logic.

---

## Related Documents

- [`../functionality/styling.md`](../functionality/styling.md) — the **F-008 static visual styling** feature guide; the prose companion to this reference.
- [`html-structure.md`](html-structure.md) — the DOM element / ID reference for the markup that these selectors target (sibling document).
- [`../index.md`](../index.md) — back to the Documentation Hub.
