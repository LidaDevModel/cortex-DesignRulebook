---
paths: "**/*.{css,scss,tsx,jsx,vue,svelte,html}"
---

# Design Fleet -- Color & Contrast

Every color must have a job. Directives only — the theory is in the weights.

## Color Space

- `color-use-oklch` -- Build palettes in OKLCH, not HSL.
- `color-reduce-chroma-at-extremes` -- Reduce chroma as lightness approaches white or black.

## Palette Structure

- `color-tint-your-neutrals` -- Add a hint of brand hue to all grays (chroma 0.005-0.01 in OKLCH).
- `color-no-pure-black-or-white` -- No `#000`/`#fff` as defaults — always tint, even subtly. Exception: forced-colors / high-contrast accessibility modes, where pure values are required.
- `color-60-30-10` -- 60% neutral backgrounds and whitespace, 30% secondary colors (text, borders, inactive states), 10% accent (CTAs, focus, highlights).
- `color-one-accent-is-enough` -- One accent color; add secondary/tertiary accents only when genuinely needed.
- `color-semantic-roles` -- Every palette needs: primary (brand, CTAs), neutral (text, backgrounds, borders — 9-11 shades), semantic (success, error, warning, info — 2-3 shades each), surface (cards, modals — 2-3 elevation levels).

## Contrast & Accessibility

- `color-wcag-body-text` -- Body text: 4.5:1 minimum (AA), target 7:1 (AAA).
- `color-wcag-large-text` -- Large text (18px+ or 14px bold): 3:1 minimum.
- `color-wcag-ui-components` -- UI components and icons: 3:1 minimum against adjacent colors.
- `color-placeholder-needs-contrast` -- Placeholders still need 4.5:1 — the usual light gray fails.

## Dangerous Combinations

- `color-no-gray-on-color` -- Never gray text on a colored background. Use a darker shade of the background color, or white with reduced opacity.
- `color-no-red-on-green` -- Never red/green as the sole differentiator — 8% of men can't distinguish them.
- `color-no-yellow-on-white` -- Yellow text on white almost always fails contrast.
- `color-no-thin-text-on-images` -- No thin light text over images. Add a scrim (gradient overlay) or a solid background.

## Dark Mode

- `color-dark-not-inverted` -- Dark mode is not inverted light mode — redesign, don't flip.
- `color-dark-elevation-by-lightness` -- Depth comes from lighter surfaces, not shadows. Higher elevation = lighter background. This applies to interactive states too: a `hover:shadow-*` that reads fine in light mode can become invisible once dark-mode surfaces sit close in lightness to their background (shadows darken, and there's little darkness left to add). Give dark-mode hover/focus/active states their own lighter background token instead of relying on shadow — pair `dark:hover:bg-[var(--your-hover-token)]` with `dark:hover:shadow-none`, keeping the shadow-only version for light mode. Note: if the element's background is set via inline `style`, it will always beat a `hover:` utility class for that property — move the base background into a class first, or the hover override silently does nothing.
- `color-dark-desaturate-accents` -- Slightly desaturate accent colors in dark mode.
- `color-dark-reduce-font-weight` -- Reduce font weight by one step in dark mode (400 → 350 for variable fonts; for static fonts step to the next lighter named weight, e.g. 300).
- `color-dark-never-pure-black-bg` -- Dark backgrounds are dark gray (OKLCH lightness 12-18%), not `#000`.

## Token Architecture

- `color-two-layer-tokens` -- Use primitive tokens (`--blue-500`) and semantic tokens (`--color-primary: var(--blue-500)`). For dark mode, redefine only the semantic layer.
