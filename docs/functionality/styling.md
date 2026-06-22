# Styling

Feature **F-008 Static Visual Styling** — Presentation layer.

## Purpose

This guide documents the **static visual styling** of the Student Report Generator — the layout, color palette, and table styling defined in the application's stylesheet `style.css` [Readme.md:L91-L155]. It is the prose companion to the full selector reference at [`../api-reference/css-reference.md`](../api-reference/css-reference.md): that page catalogs *what* each rule declares, while this guide explains *how* the styling is organized by concern and *what behavior is expected* from it. All content is code-grounded in the repository-root `Readme.md`, and every claim carries an inline `[Readme.md:Lx-Ly]` citation.

---

## Source Location

- **Stylesheet:** the embedded `style.css` block [Readme.md:L91-L155], which contains exactly eleven rule-blocks.
- **Stylesheet link:** the markup loads it via `<link rel="stylesheet" href="style.css">` [Readme.md:L35].
- **Viewport meta:** the application's only responsiveness directive lives in the markup `<head>` [Readme.md:L32].

---

## F-008 Static Visual Styling

The stylesheet is purely declarative CSS that paints the single-page application; it contributes no behavior. Its eleven rule-blocks [Readme.md:L91-L155] group into three concerns — layout, color palette, and table styling.

### Layout

The application renders as a **centered white card** on a padded body. The `body` rule sets the base typography and a light-grey page background with a 20px gutter [Readme.md:L92-L97], while `.container` caps the card at 800px wide, centers it with `margin: auto`, and gives it rounded corners and a soft drop shadow [Readme.md:L99-L106]:

```css
max-width: 800px;
margin: auto;
```

The input form is laid out by `.form-section` as a vertical CSS grid with 10px gaps between fields and a 30px gap below it [Readme.md:L112-L116], and both headings are centered by the grouped `h1, h2` rule [Readme.md:L108-L110]. The form fields themselves are padded and use a 16px font for legibility [Readme.md:L118-L121].

### Palette

The stylesheet uses a small, fixed palette. The page background is light grey, and the primary action buttons (Generate Report / Download PDF) are blue [Readme.md:L123-L130], darkening to a deeper blue on hover [Readme.md:L132-L134]. The three literal surface/action colors are:

| Color | Hex | Used by | Source |
|---|---|---|---|
| Page background (light grey) | `#f4f6f8` | `body` | [Readme.md:L94] |
| Action button (blue) | `#007bff` | `button` | [Readme.md:L125] |
| Button hover (deeper blue) | `#0056b3` | `button:hover` | [Readme.md:L133] |

The card itself is white with a soft shadow rendered at `rgba(0,0,0,0.1)` [Readme.md:L102-L105]. The greys used for separators and borders are `#ddd` on the report-card top border [Readme.md:L136-L139] and `#ccc` on the table grid [Readme.md:L147-L149].

### Table styling

The marks table is full width with collapsed borders and a 15px top margin [Readme.md:L141-L145]. A single 1px `#ccc` border is applied to the table, header cells, and data cells together [Readme.md:L147-L149]; combined with `border-collapse: collapse` [Readme.md:L143] this yields **single (non-doubled) cell borders** rather than the browser-default doubled edges. Header and data cells are padded and center-aligned [Readme.md:L151-L154]. Above the table, the `.report-card` section is set apart from the input form by a 2px `#ddd` top border [Readme.md:L136-L139].

> For the complete selector-by-selector reference of all eleven rule-blocks, see [`../api-reference/css-reference.md`](../api-reference/css-reference.md); this guide intentionally does not duplicate the full table.

---

## Responsiveness

The application's **only** responsiveness mechanism is the viewport `<meta>` tag declared in the markup `<head>` [Readme.md:L32]:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

There are **no `@media` query rules anywhere** in `style.css` [Readme.md:L91-L155]. The layout's fluid behavior comes entirely from the centered, capped container — `.container { max-width: 800px; margin: auto; }` [Readme.md:L99-L106], whose cap is `max-width: 800px` [Readme.md:L100] — working together with the grid-based `.form-section` [Readme.md:L112-L116] and the `width: 100%` marks table [Readme.md:L141-L145]. In practice the card shrinks fluidly on narrow viewports and stays centered and capped at 800px on wide ones, but it is **not breakpoint-responsive**.

The README markets the project as having a **"Responsive UI"** [Readme.md:L253]. Stated accurately, that responsiveness is the viewport meta tag [Readme.md:L32] plus the fluid `max-width: 800px` container [Readme.md:L100] — **not** media-query breakpoints, of which there are none. The same accurate framing appears in the [`../api-reference/css-reference.md`](../api-reference/css-reference.md) reference.

---

## Expected Behavior / Contract

The "expectation from the code" for F-008 is that styling is **entirely static and presentational** — it describes appearance and never participates in logic.

| Aspect | Expectation |
|---|---|
| **Static & deterministic presentation** | Styling is purely declarative CSS. With the stylesheet applied, the app always renders as a centered white card (≤ 800px) on a light-grey background, with vertically stacked padded inputs, blue action buttons that darken to `#0056b3` on hover, and a center-aligned, single-bordered, full-width marks table beneath a top-bordered report card [Readme.md:L92-L154]. |
| **No JS-driven style changes** | The JavaScript never adds or removes classes or inline styles; presentation does not change at runtime based on logic. The only runtime DOM mutation is *content* (via `.innerHTML` / `.innerText`), never *style* [Readme.md:L161-L238]. |
| **Responsiveness** | Achieved by the viewport `<meta>` tag plus the fluid `max-width: 800px` container only; there are no media-query breakpoints [Readme.md:L32], [Readme.md:L91-L155]. |
| **Presentation independence** | Styling has no effect on data entry, computation, grading, report-content rendering, or PDF export — those run identically with or without the stylesheet and are documented separately. |
| **Precondition** | The stylesheet must be linked via `<link rel="stylesheet" href="style.css">` [Readme.md:L35]. If it is absent or fails to load, the app falls back to unstyled browser defaults but remains fully functional. |

---

## Related Documents

- [`../api-reference/css-reference.md`](../api-reference/css-reference.md) — the full selector / style-declaration reference; the precise companion to this guide.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the application's behavioral invariants and preconditions; note that F-008 styling is static and non-behavioral, so it contributes no runtime invariants.
- [`report-rendering.md`](report-rendering.md) — F-006 on-screen rendering, which produces the rendered-DOM report card that these styles target.
- [`../index.md`](../index.md) — back to the Documentation Hub.
