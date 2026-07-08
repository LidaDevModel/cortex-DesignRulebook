---
paths: "**/*.{css,scss,tsx,jsx,vue,svelte,html}"
---

# Design Fleet -- Visual Design

10 rules that separate considered interfaces from default ones. Apply when writing any visual CSS.

> **Enforced deterministically:** the design lint catches V1 (pure black shadows), V5 (pure `#000`/`#fff` defaults), V7 (off-scale spacing), and T1 (invisible default fonts as primary). These are variance-class rules — known but slipped under build pressure; the lint is the backstop, not an excuse to skip them.

## Border Radius

- `visual-concentric-radius` -- Inner radius = outer radius minus padding. Same radius on nested elements creates uneven curves.

## Shadows

- `visual-layered-shadows` -- Layer multiple shadows with increasing blur and decreasing opacity. Single shadows look flat.
- `visual-shadow-direction` -- All shadows share the same offset direction (single light source). Mixed directions feel broken.
- `visual-no-pure-black-shadow` -- Use neutral colors for shadows, not pure black. `rgba(17, 24, 39, 0.08)` over `rgba(0, 0, 0, 0.25)`.
- `visual-shadow-matches-elevation` -- Larger blur and offset = higher elevation. Use a consistent shadow scale (`--shadow-1`, `--shadow-2`, `--shadow-3`).
- `visual-animate-shadow-pseudo` -- Never transition `box-shadow` directly. Animate opacity on a pseudo-element with the target shadow instead.
- `visual-shadow-not-clipped` -- A shadow paints *outside* the element's border box, so an ancestor with `overflow: hidden` (or `clip`/`auto`/`scroll`) **amputates it**. This is a variance-class bug: `overflow-hidden` gets added reflexively to round a child's corners or make a scroll area, silently cutting the shadow of everything inside. Separate the clip from the shadow — never put both jobs on the same boundary.
- `visual-dark-hover-needs-lightness-not-shadow` -- A `hover:shadow-*` elevation cue can go invisible in dark mode once card/surface backgrounds sit close in lightness to the page behind them — see `color-dark-elevation-by-lightness` in `color-contrast.mdc` for the fix (a dark-mode-only lighter background step, not a darker shadow).

```jsx
/* Incorrect: the rounding wrapper clips the card's shadow */
<div className="rounded-2xl overflow-hidden">
  <Card className="shadow-lg" />        {/* ✂ shadow cut at the wrapper edge */}
</div>

/* Correct: outer carries shadow + radius (NO overflow); inner clips */
<div className="rounded-2xl shadow-lg">
  <div className="rounded-2xl overflow-hidden"><img /></div>
</div>
```

Other fixes by cause: if nothing actually needs clipping, drop `overflow-hidden`; for a scroll area, pad the scroll container by ≥ the shadow's reach (blur + spread + offset) so the shadow lands inside the clip box. Reliable detection is at runtime (rendered bounds vs the clipping ancestor) — catch it in the live preview, not by eye.

```css
/* Incorrect: single pure-black shadow */
box-shadow: 0 4px 8px rgba(0, 0, 0, 0.25);

/* Correct: layered, tinted, increasing blur / decreasing opacity */
box-shadow:
  0 1px 2px rgba(15, 23, 42, 0.06),
  0 4px 8px rgba(15, 23, 42, 0.08),
  0 12px 24px rgba(15, 23, 42, 0.05);
```

## Spacing

- `visual-consistent-spacing-scale` -- Use a defined scale (4, 8, 12, 16, 24, 32, 48, 64), never arbitrary pixel values. The lint enforces this exact set.

```css
/* Incorrect: off-scale value */
padding: 13px;

/* Correct: nearest step on the scale */
padding: 12px; /* or 16px */
```

## Borders

- `visual-border-alpha-colors` -- Semi-transparent borders (`var(--gray-a4)`) adapt to any background. Hardcoded hex borders break on theme changes.

## Buttons

- `visual-button-shadow-anatomy` -- Polished buttons use six layers: outer cut shadow, inner ambient highlight, inner top highlight, layered depth shadows, text drop-shadow, subtle gradient background.
