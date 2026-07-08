---
paths: "**/*.{css,scss,tsx,jsx,vue,svelte,html}"
---

# Design Fleet -- Responsive Design

How interfaces adapt to screens, input methods, and device capabilities.

## Strategy

- `responsive-mobile-first` -- Write base styles for mobile, use `min-width` queries to layer complexity. Desktop-first means mobile loads unnecessary styles. Exception: a `VISION.md` north star that declares the product desktop-first (e.g. a desktop demo) overrides this default.
- `responsive-content-driven-breakpoints` -- Don't chase device sizes. Start narrow, stretch until the design breaks, add a breakpoint there. Three breakpoints usually suffice.
- `responsive-container-over-viewport` -- Use container queries (`@container`) for component-level responsiveness. Viewport queries are for page layouts. A card in a sidebar should adapt to its container, not the window.

## Input Detection

- `responsive-detect-pointer` -- Use `@media (pointer: coarse)` for touch devices (larger padding, bigger targets) and `@media (pointer: fine)` for mouse/trackpad. Screen size doesn't tell you input method.
- `responsive-detect-hover` -- Use `@media (hover: hover)` for hover effects. Touch devices can't hover — don't rely on hover for functionality.
- `responsive-no-hover-only-actions` -- Never hide actions behind hover. Touch users will never discover them. Use hover to enhance, not to reveal.

## Safe Areas

- `responsive-safe-area-insets` -- Use `env(safe-area-inset-*)` for padding on modern phones with notches, rounded corners, and home indicators.
- `responsive-viewport-fit-cover` -- Set `viewport-fit=cover` in the viewport meta tag to enable safe area insets.

## Images

- `responsive-srcset-and-picture` -- Use `srcset` + `sizes` (width descriptors) for resolution switching; reach for `<picture>` + `<source media>` only when you need different crops/compositions (art direction), not just different resolutions.

## Layout Adaptation

- `responsive-adapt-dont-amputate` -- Never hide critical functionality on mobile. Adapt the interface — restructure, reflow, collapse — but don't remove features.
- `responsive-tables-to-cards` -- Transform data tables into card layouts on narrow screens using `display: block` and `data-label` attributes. Tables don't shrink gracefully.
- `responsive-navigation-stages` -- Three stages: hamburger + drawer on mobile, horizontal compact on tablet, full with labels on desktop.

## Viewport meta

- `responsive-never-disable-zoom` -- Never use `user-scalable=no`. It breaks accessibility. If the layout breaks at 200% zoom, fix the layout.
