# Styling

## Purpose

This guide documents feature **F-008 static visual styling** — the static visual presentation of the Student Report Generator: the page layout, color palette, and marks-table styling declared in `style.css` `[Readme.md:L91-L155]`. It is the prose companion to the exhaustive selector table in [`../api-reference/css-reference.md`](../api-reference/css-reference.md); all content here is **code-grounded**, extracted from the source embedded in the repository-root `Readme.md`, with an inline `[Readme.md:Lx-Ly]` citation on every claim.

---

## Source Location

- **Stylesheet:** the complete `style.css` is embedded as a fenced block at `[Readme.md:L91-L155]` and contains exactly eleven rule-blocks.
- **Viewport meta:** the responsiveness-related `<meta name="viewport" ...>` tag lives in the markup at `[Readme.md:L32]`.
- **Stylesheet link:** the stylesheet is attached to the page via `<link rel="stylesheet" href="style.css">` at `[Readme.md:L35]`.

---

## F-008 Static Visual Styling

The presentation layer is implemented as a single, entirely static stylesheet `[Readme.md:L91-L155]`. Its eleven rule-blocks group naturally into three concerns — **layout**, **palette**, and **table styling** — described below. For the complete selector-by-selector reference, see [`../api-reference/css-reference.md`](../api-reference/css-reference.md); this guide intentionally does not duplicate the full table.

### Layout

The entire application is wrapped in a centered `.container` "card" capped at `max-width: 800px` and horizontally centered with `margin: auto`, sitting on a `body` that supplies Arial typography and a 20px page gutter `[Readme.md:L92-L106]`. The input form is rendered as a single-column CSS grid with 10px gaps between fields and 30px of spacing below it `[Readme.md:L112-L116]`, and both the app title (`<h1>`) and the report heading (`<h2>`) are centered `[Readme.md:L108-L110]`. The card itself has 30px of internal padding, rounded 10px corners, and a soft drop shadow `[Readme.md:L99-L106]`.

### Palette

The page sits on a light-grey background, and the action buttons use a primary blue that darkens on hover, all inside a white card with a soft drop shadow:

- Page background `#f4f6f8` `[Readme.md:L94]`.
- Primary blue buttons `#007bff` `[Readme.md:L125]` that darken to `#0056b3` on hover `[Readme.md:L132-L134]`.
- White card with a soft `rgba(0,0,0,0.1)` drop shadow `[Readme.md:L102-L105]`.

```css
background: #f4f6f8;   /* body         */
background: #007bff;   /* button       */
background: #0056b3;   /* button:hover */
```
*The three palette anchors, verbatim from the source `[Readme.md:L94]`, `[Readme.md:L125]`, `[Readme.md:L133]`.*

### Table styling

The marks table is full-width, uses collapsed borders, and has 15px of top margin `[Readme.md:L141-L145]`. A 1px `#ccc` border is applied to the table and to every header and data cell `[Readme.md:L147-L149]`, and all cells are padded and center-aligned `[Readme.md:L151-L154]`. Because `border-collapse: collapse` `[Readme.md:L143]` combines with the shared `table, th, td { border: 1px solid #ccc; }` rule `[Readme.md:L147-L149]`, adjacent cell borders merge into single (non-doubled) 1px lines:

```css
border-collapse: collapse;   /* table         */
border: 1px solid #ccc;      /* table, th, td  */
```

The rendered report is visually separated from the form above it by a 2px `#ddd` top border on `.report-card` `[Readme.md:L136-L139]`.

---

## Responsiveness

The application's **only** responsiveness mechanism is the viewport `<meta>` tag in the markup `[Readme.md:L32]`:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

There are **no `@media` query rules** anywhere in `style.css` `[Readme.md:L91-L155]` — and likewise no flexbox, animation, or transition rules. The adaptive feel comes entirely from the **fluid**, width-capped `.container` (`max-width: 800px; margin: auto;`) `[Readme.md:L99-L106]` together with the single-column grid form `[Readme.md:L112-L116]`: the card scales fluidly with the viewport up to its 800px cap and remains centered at all widths.

The project README markets a "Responsive UI" `[Readme.md:L253]`. To state this precisely: the layout is **fluid and centered** (viewport meta plus a width-capped container), but it is **not breakpoint-responsive** — no media queries reflow or restyle the page at specific screen widths. For the full selector reference that confirms the absence of any `@media` rules, see [`../api-reference/css-reference.md`](../api-reference/css-reference.md).

---

## Expected Behavior / Contract

This block makes explicit the **expectation from the code** for F-008. Styling is purely presentational and carries the following guarantees:

| Aspect | Expectation / Contract |
|---|---|
| **Static & deterministic presentation** | Styling is purely declarative CSS. With the stylesheet applied, the app always renders as a centered white card (≤ 800px) on a light-grey background, with vertically stacked padded inputs, blue buttons that darken on hover, and a bordered, center-aligned, full-width marks table beneath a top-bordered report card `[Readme.md:L92-L154]`. |
| **No JS-driven style changes** | The JavaScript never adds or removes classes and never sets inline styles; presentation does not change at runtime based on logic `[Readme.md:L161-L238]`. The only DOM mutation is **content** — via `.innerHTML` `[Readme.md:L176-L190]` and `.innerText` `[Readme.md:L208-L212]` — never style. |
| **Responsiveness** | Limited to the viewport meta `[Readme.md:L32]` plus the fluid `max-width: 800px` container `[Readme.md:L99-L106]`; there are **no media-query breakpoints** `[Readme.md:L91-L155]`. |
| **Presentation independence** | Styling has no effect on computation, grading, report-content rendering, or PDF export — it is presentation-only and fully decoupled from the application logic `[Readme.md:L161-L238]`. |
| **Precondition (stylesheet link)** | The stylesheet must be attached via `<link rel="stylesheet" href="style.css">` `[Readme.md:L35]`. If it is absent, the app falls back to unstyled browser defaults but remains fully functional — data entry, computation, grading, rendering, and PDF export are unaffected. |

---

## Related Documents

- [`../api-reference/css-reference.md`](../api-reference/css-reference.md) — the full selector / style reference (the exhaustive companion to this guide).
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the application's behavioral invariants and preconditions (note: styling is static and non-behavioral).
- [`report-rendering.md`](report-rendering.md) — feature F-006, the rendered report-card DOM that these styles target.
- [`../index.md`](../index.md) — back to the Documentation Hub.
