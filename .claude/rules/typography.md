---
paths: "**/*.{css,scss,tsx,jsx,vue,svelte,html}"
---

# Design Fleet -- Typography

26 rules for typographically considered interfaces. Apply when setting any font or text property.

## Sizing & Scale

- `type-fluid-headings` -- Use `clamp(min, preferred, max)` for headings on marketing/content pages, e.g. `clamp(2rem, 5vw + 1rem, 4rem)`. Include a rem offset so text doesn't collapse to zero on small screens.
- `type-fixed-rem-for-apps` -- Use fixed `rem` scales for app UI, dashboards, and data-dense interfaces. No major design system uses fluid type in product UI.
- `type-fewer-sizes-more-contrast` -- 5 sizes maximum *in the scale*: xs (captions), sm (metadata), base (body), lg (subheadings), xl+ (headlines) — and any single screen uses only 2-3 of them (see `layout-composition`). Not 14/15/16/18px — near-identical sizes make muddy hierarchy.
- `type-vertical-rhythm` -- Line-height is the base unit for vertical spacing: `line-height: 1.5` on 16px body = 24px, so spacing values are multiples of 24px.
- `type-measure-in-ch` -- `max-width: 65ch` for readable line lengths. Narrow columns need tighter leading, wide columns more.

## Font Selection

- `type-avoid-invisible-defaults` -- Inter, Roboto, Open Sans, Lato, Montserrat read as generic defaults. Use them only when personality isn't the goal.
  Enforced: the deterministic lint flags Inter/Roboto/Arial/Open Sans/Lato/system-ui as the primary family (T1).
- `type-better-alternatives` -- Instead of Inter → Instrument Sans, Plus Jakarta Sans, Outfit. Instead of Roboto → Onest, Figtree, Urbanist. Instead of Open Sans → Source Sans 3, Nunito Sans, DM Sans. For editorial → Fraunces, Newsreader, Lora. Exception: when the project's `VISION.md` locks font families, never introduce new families — work within the locked ramp.
- `type-one-family-is-enough` -- One family in multiple weights beats two competing typefaces. Add a second font only for genuine contrast (display headlines + body serif).
- `type-never-pair-similar` -- Never pair two fonts that are similar but not identical (two geometric sans-serifs).
- `type-dark-mode-lighter-weight` -- Reduce font weight by one step in dark mode.

## Numeric Formatting

- `type-tabular-nums-for-data` -- `font-variant-numeric: tabular-nums` for columns, dashboards, pricing.
- `type-oldstyle-nums-for-prose` -- `font-variant-numeric: oldstyle-nums` in body text.
- `type-slashed-zero` -- `font-variant-numeric: slashed-zero` in code-adjacent UIs.
- `type-proper-fractions` -- `font-variant-numeric: diagonal-fractions` for typographic fractions.

## OpenType Features

- `type-opentype-contextual-alternates` -- Keep `font-feature-settings: "calt" 1` enabled.
- `type-disambiguation-stylistic-set` -- Enable `ss02` in code-facing UIs to distinguish I/l/1 and 0/O.

## Rendering

- `type-optical-sizing-auto` -- Leave `font-optical-sizing: auto` for size-adaptive glyph shapes.
- `type-antialiased-on-retina` -- Set `-webkit-font-smoothing: antialiased` and `-moz-osx-font-smoothing: grayscale` on body.
- `type-no-font-synthesis` -- Set `font-synthesis: none` on display/icon fonts to prevent faux bold/italic.
- `type-font-display-swap` -- Use `font-display: swap` in `@font-face` so text renders immediately with fallback.

## Text Wrapping

- `type-text-wrap-balance-headings` -- `text-wrap: balance` on headings.
- `type-text-wrap-pretty` -- `text-wrap: pretty` on body text to reduce orphans.
- `type-justify-with-hyphens` -- Pair `text-align: justify` with `hyphens: auto`.

## Spacing & Decoration

- `type-underline-offset` -- `text-underline-offset: 3px` with `text-decoration-skip-ink: auto`.
- `type-letter-spacing-uppercase` -- Add `letter-spacing: 0.05em` to uppercase and small-caps text.
- `type-variable-weight-continuous` -- Variable fonts accept any integer 100-900: use `font-weight: 450` or `550`, not just standard stops.

## Quick Reference

| Property | Use Case | Value |
|----------|----------|-------|
| `font-variant-numeric: tabular-nums` | Data tables, pricing | Fixed-width digits |
| `font-variant-numeric: oldstyle-nums` | Body text | Blends with lowercase |
| `font-variant-numeric: slashed-zero` | Code UIs | Distinguishes 0 from O |
| `font-feature-settings: "ss02"` | Code UIs | Disambiguates I/l/1 |
| `font-optical-sizing: auto` | Everywhere | Size-adaptive glyphs |
| `-webkit-font-smoothing: antialiased` | Retina displays | Thinner, cleaner text |
| `text-wrap: balance` | Headings | Even line lengths |
| `text-underline-offset: 3px` | Links | Clear descender space |
| `font-synthesis: none` | Display/icon fonts | Prevents faux styles |
