# Styling

Feature **F-008 Static Visual Styling** — Presentation layer.

## Purpose

This guide documents the **static visual styling** of the Student Report Generator — the layout, color palette, and table styling defined in the application's stylesheet `style.css` [Readme.md:L106-L170]. It is the prose companion to the full selector reference at [`../api-reference/css-reference.md`](../api-reference/css-reference.md): that page catalogs *what* each rule declares, while this guide explains *how* the styling is organized by concern and *what behavior is expected* from it. All content is code-grounded in the repository-root `Readme.md`, and every claim carries an inline `Readme.md` line-range citation.

---

## Source Location

- **Stylesheet:** the embedded `style.css` block [Readme.md:L106-L170], which contains exactly eleven rule-blocks.
- **Stylesheet link:** the markup loads it via `<link rel="stylesheet" href="style.css">` [Readme.md:L50].
- **Viewport meta:** the application's only responsiveness directive lives in the markup `<head>` [Readme.md:L47].

---

## How It Works

The presentation layer (**F-008**) is **static, declarative CSS** that paints the single-page app and contributes **no behavior**. The browser loads the stylesheet via the markup `<link>` [Readme.md:L50] and applies its eleven rule-blocks [Readme.md:L106-L170]; the JavaScript never adds or removes classes or inline styles, so the styling does not change at runtime — the only runtime DOM mutation is *content* (via `.innerHTML` / `.innerText`), never *style* [Readme.md:L176-L253]. The rules organize into three concerns: **layout** — the light-grey `body`, the centered white `.container` capped at `max-width: 800px`, the grid-based `.form-section`, centered headings, and padded `input`s [Readme.md:L107-L136]; the **color palette** — blue action buttons that darken on hover, over light-grey surfaces [Readme.md:L138-L149]; and **table styling** — the full-width, collapsed-border marks table with single 1px `#ccc` cell borders beneath a top-bordered `.report-card` [Readme.md:L151-L169]. Each concern is detailed below.

---

## F-008 Static Visual Styling

The stylesheet is purely declarative CSS that paints the single-page application; it contributes no behavior. Its eleven rule-blocks [Readme.md:L106-L170] group into three concerns — layout, color palette, and table styling.

### Layout

The application renders as a **centered white card** on a padded body. The `body` rule sets the base typography and a light-grey page background with a 20px gutter [Readme.md:L107-L112], while `.container` caps the card at 800px wide, centers it with `margin: auto`, and gives it rounded corners and a soft drop shadow [Readme.md:L114-L121]:

```css
max-width: 800px;
margin: auto;
```

The input form is laid out by `.form-section` as a vertical CSS grid with 10px gaps between fields and a 30px gap below it [Readme.md:L127-L131], and both headings are centered by the grouped `h1, h2` rule [Readme.md:L123-L125]. The form fields themselves are padded and use a 16px font for legibility [Readme.md:L133-L136].

### Palette

The stylesheet uses a small, fixed palette. The page background is light grey, and the primary action buttons (Generate Report / Download PDF) are blue [Readme.md:L138-L145], darkening to a deeper blue on hover [Readme.md:L147-L149]. The three literal surface/action colors are:

| Color | Hex | Used by | Source |
|---|---|---|---|
| Page background (light grey) | `#f4f6f8` | `body` | [Readme.md:L109] |
| Action button (blue) | `#007bff` | `button` | [Readme.md:L140] |
| Button hover (deeper blue) | `#0056b3` | `button:hover` | [Readme.md:L148] |

The card itself is white with a soft shadow rendered at `rgba(0,0,0,0.1)` [Readme.md:L117-L120]. The greys used for separators and borders are `#ddd` on the report-card top border [Readme.md:L151-L154] and `#ccc` on the table grid [Readme.md:L162-L164].

### Table styling

The marks table is full width with collapsed borders and a 15px top margin [Readme.md:L156-L160]. A single 1px `#ccc` border is applied to the table, header cells, and data cells together [Readme.md:L162-L164]; combined with `border-collapse: collapse` [Readme.md:L158] this yields **single (non-doubled) cell borders** rather than the browser-default doubled edges. Header and data cells are padded and center-aligned [Readme.md:L166-L169]. Above the table, the `.report-card` section is set apart from the input form by a 2px `#ddd` top border [Readme.md:L151-L154].

> For the complete selector-by-selector reference of all eleven rule-blocks, see [`../api-reference/css-reference.md`](../api-reference/css-reference.md); this guide intentionally does not duplicate the full table.

---

## Responsiveness

The application's **only** responsiveness mechanism is the viewport `<meta>` tag declared in the markup `<head>` [Readme.md:L47]:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

There are **no `@media` query rules anywhere** in `style.css` [Readme.md:L106-L170]. The layout's fluid behavior comes entirely from the centered, capped container — `.container { max-width: 800px; margin: auto; }` [Readme.md:L114-L121], whose cap is `max-width: 800px` [Readme.md:L115] — working together with the grid-based `.form-section` [Readme.md:L127-L131] and the `width: 100%` marks table [Readme.md:L156-L160]. In practice the card shrinks fluidly on narrow viewports and stays centered and capped at 800px on wide ones, but it is **not breakpoint-responsive**.

The README markets the project as having a **"Responsive UI"** [Readme.md:L268]. Stated accurately, that responsiveness is the viewport meta tag [Readme.md:L47] plus the fluid `max-width: 800px` container [Readme.md:L115] — **not** media-query breakpoints, of which there are none. The same accurate framing appears in the [`../api-reference/css-reference.md`](../api-reference/css-reference.md) reference.

---

## Expected Behavior / Contract

The "expectation from the code" for F-008 is that styling is **entirely static and presentational** — it describes appearance and never participates in logic.

| Aspect | Expectation |
|---|---|
| **Static & deterministic presentation** | Styling is purely declarative CSS. With the stylesheet applied, the app always renders as a centered white card (≤ 800px) on a light-grey background, with vertically stacked padded inputs, blue action buttons that darken to `#0056b3` on hover, and a center-aligned, single-bordered, full-width marks table beneath a top-bordered report card [Readme.md:L107-L169]. |
| **No JS-driven style changes** | The JavaScript never adds or removes classes or inline styles; presentation does not change at runtime based on logic. The only runtime DOM mutation is *content* (via `.innerHTML` / `.innerText`), never *style* [Readme.md:L176-L253]. |
| **Responsiveness** | Achieved by the viewport `<meta>` tag plus the fluid `max-width: 800px` container only; there are no media-query breakpoints [Readme.md:L47], [Readme.md:L106-L170]. |
| **Presentation independence** | Styling has no effect on data entry, computation, grading, report-content rendering, or PDF export — those run identically with or without the stylesheet and are documented separately. |
| **Precondition** | The stylesheet must be linked via `<link rel="stylesheet" href="style.css">` [Readme.md:L50]. If it is absent or fails to load, the app falls back to unstyled browser defaults but remains fully functional. |

---

## Related Documents

- [`../api-reference/css-reference.md`](../api-reference/css-reference.md) — the full selector / style-declaration reference; the precise companion to this guide.
- [`../contracts/behavioral-contracts.md`](../contracts/behavioral-contracts.md) — the application's behavioral invariants and preconditions; note that F-008 styling is static and non-behavioral, so it contributes no runtime invariants.
- [`report-rendering.md`](report-rendering.md) — F-006 on-screen rendering, which produces the rendered-DOM report card that these styles target.
- [`../index.md`](../index.md) — back to the Documentation Hub.
