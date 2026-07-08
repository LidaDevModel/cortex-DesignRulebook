# Responsive Design Reference

Deep implementation guide for mobile-first CSS, container queries, input detection, safe areas, and responsive images. For principles and rules, see the `responsive-design.mdc` rule. This file covers code patterns and implementation details.

## Container Queries

Viewport queries are for page layouts. Container queries are for components:

```css
.card-container {
  container-type: inline-size;
}

.card {
  display: grid;
  gap: var(--space-md);
}

@container (min-width: 400px) {
  .card {
    grid-template-columns: 120px 1fr;
  }
}
```

A card in a narrow sidebar stays compact. The same card in a main content area expands — automatically, without viewport hacks.

## Input Method Detection

```css
/* Fine pointer (mouse, trackpad) */
@media (pointer: fine) {
  .button { padding: 8px 16px; }
}

/* Coarse pointer (touch, stylus) */
@media (pointer: coarse) {
  .button { padding: 12px 20px; }
}

/* Device supports hover */
@media (hover: hover) {
  .card:hover { transform: translateY(-2px); }
}

/* Device doesn't support hover (touch) */
@media (hover: none) {
  .card { /* No hover state — use active instead */ }
}
```

## Safe Areas

```css
body {
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}

.footer {
  padding-bottom: max(1rem, env(safe-area-inset-bottom));
}
```

Enable in viewport meta:

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
```

## Responsive Images

### Resolution Switching with srcset

```html
<img
  src="hero-800.jpg"
  srcset="
    hero-400.jpg 400w,
    hero-800.jpg 800w,
    hero-1200.jpg 1200w
  "
  sizes="(max-width: 768px) 100vw, 50vw"
  alt="Hero image"
>
```

`srcset` lists available images with actual widths. `sizes` tells the browser how wide the image displays. The browser picks the best file for viewport width and pixel density.

### Art Direction with picture

```html
<picture>
  <source media="(min-width: 768px)" srcset="wide-crop.jpg">
  <source media="(max-width: 767px)" srcset="tall-crop.jpg">
  <img src="fallback.jpg" alt="Product photo">
</picture>
```

Use `<picture>` when you need different crops or compositions — not just different resolutions.

## Self-Adjusting Grid

```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: var(--space-md);
}
```

Columns are at least 280px, as many as fit per row, leftovers stretch. No breakpoints needed.

## Table Adaptation on Mobile

```css
@media (max-width: 640px) {
  table, thead, tbody, tr, th, td {
    display: block;
  }

  thead { display: none; }

  td::before {
    content: attr(data-label);
    font-weight: 600;
    display: block;
    margin-bottom: 4px;
  }

  tr + tr {
    margin-top: var(--space-md);
    padding-top: var(--space-md);
    border-top: 1px solid var(--gray-200);
  }
}
```

```html
<td data-label="Status">Active</td>
```

## Navigation Stages

| Viewport | Pattern |
|----------|---------|
| Mobile (<640px) | Hamburger icon + slide-out drawer |
| Tablet (640-1024px) | Horizontal bar, icons only or abbreviated labels |
| Desktop (>1024px) | Full horizontal nav with labels, possibly sidebar |

Use `<details>`/`<summary>` for collapsible sections on mobile — no JavaScript required.
