# Color & Contrast Reference

Deep implementation guide for color systems, palettes, dark mode, and accessibility testing. For principles and rules, see the `color-contrast.mdc` rule. This file covers how to implement them in code.

## OKLCH in Practice

OKLCH is a perceptually uniform color space. Unlike HSL, equal lightness steps actually look equal.

```css
/* OKLCH: lightness (0-100%), chroma (0-0.4+), hue (0-360) */
--color-primary: oklch(60% 0.15 250);
--color-primary-light: oklch(85% 0.08 250);
--color-primary-dark: oklch(35% 0.12 250);
```

As you move toward white or black, reduce chroma. A light blue at 85% lightness needs ~0.08 chroma, not the 0.15 of your base.

### Building a Palette with OKLCH

```css
:root {
  /* Brand */
  --brand: oklch(55% 0.18 250);

  /* Tinted neutrals — brand hue at minimal chroma */
  --gray-50:  oklch(97% 0.005 250);
  --gray-100: oklch(95% 0.008 250);
  --gray-200: oklch(88% 0.008 250);
  --gray-300: oklch(78% 0.008 250);
  --gray-400: oklch(65% 0.008 250);
  --gray-500: oklch(55% 0.008 250);
  --gray-600: oklch(45% 0.008 250);
  --gray-700: oklch(35% 0.008 250);
  --gray-800: oklch(25% 0.01 250);
  --gray-900: oklch(18% 0.01 250);
  --gray-950: oklch(12% 0.01 250);

  /* Semantic */
  --success: oklch(65% 0.18 145);
  --error: oklch(55% 0.22 25);
  --warning: oklch(75% 0.15 85);
  --info: oklch(60% 0.15 250);
}
```

The chroma on neutrals (0.005-0.01) is barely perceptible but creates subconscious cohesion with the brand hue.

### Modern CSS Color Functions

```css
/* color-mix for derived colors */
--primary-hover: color-mix(in oklch, var(--brand), black 15%);
--primary-subtle: color-mix(in oklch, var(--brand), white 85%);

/* light-dark() for theme-aware values */
color: light-dark(var(--gray-900), var(--gray-100));
background: light-dark(var(--gray-50), var(--gray-950));
```

## Dark Mode Implementation

```css
:root {
  color-scheme: light dark;

  /* Light mode surfaces — shadows for depth */
  --surface-1: white;
  --surface-2: var(--gray-50);
  --surface-3: var(--gray-100);
  --shadow-elevation-1: 0 1px 3px oklch(0% 0 0 / 0.08);
}

[data-theme="dark"] {
  /* Dark mode surfaces — lighter = higher elevation, no shadows */
  --surface-1: var(--gray-950);
  --surface-2: var(--gray-900);
  --surface-3: var(--gray-800);
  --shadow-elevation-1: none;

  /* Desaturate accents */
  --brand: oklch(65% 0.14 250);

  /* Reduce body font weight */
  --body-weight: 350;
}
```

Key differences from light mode:
- Depth from surface lightness, not shadows
- Desaturated accents (reduce chroma by ~0.03-0.05)
- Reduced font weight (light-on-dark appears heavier)
- Never pure black backgrounds — use OKLCH lightness 12-18%

## Gray on Color — The Fix

```css
/* BAD — gray text on colored background */
.banner {
  background: oklch(55% 0.18 250);
  color: oklch(70% 0 0); /* Dead gray — washed out */
}

/* GOOD — tinted text using the background hue */
.banner {
  background: oklch(55% 0.18 250);
  color: oklch(92% 0.03 250); /* Light shade of same hue */
}

/* ALSO GOOD — white with reduced opacity */
.banner {
  background: oklch(55% 0.18 250);
  color: oklch(100% 0 0 / 0.85);
}
```

## Contrast Testing

Tools:
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- Browser DevTools → Rendering → Emulate vision deficiencies
- [Polypane](https://polypane.app/) for real-time testing

See `color-contrast.mdc` for WCAG ratio requirements (AA/AAA thresholds for body text, large text, and UI components).

## Alpha Transparency as Design Smell

Heavy use of `rgba`/`hsla` usually means an incomplete palette. Alpha creates unpredictable contrast, performance overhead, and inconsistency. Define explicit overlay colors for each context instead. Exception: focus rings and interactive states where see-through is intentional.
