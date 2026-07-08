# View Transitions API

The browser-native way to animate between two DOM states — page
navigations, lightboxes, shared-element transitions. The API does the
heavy lifting (snapshotting, crossfading, FLIP-style movement); your job
is naming the participating elements correctly and cleaning up after.

> **Delta knowledge** — Baseline models reliably miss two rules here at
> build time: `view-transition-name` uniqueness during a transition, and
> cleaning up names after completion (AUDIT.md, ui-patterns#8 — two cold
> misses). This reference is load-bearing: it is part of the package's
> knowledge-injection core, not a recap of what the model already knows.

---

### Prefer View Transitions over JS libraries (MEDIUM) (`transition-over-js-library`)

For page and shared-element transitions, the native API beats JS
animation libraries: it runs off the main thread, survives React
re-renders, and needs no library weight. Reserve `layoutId`-style JS
solutions for cases the API cannot express.

**Incorrect (JS-based transition):**

```tsx
import { motion } from "motion/react";

function ImageLightbox() {
  return (
    <motion.img layoutId="hero" />
  );
}
```

**Correct (native View Transition):**

```ts
function openLightbox(img: HTMLImageElement) {
  img.style.viewTransitionName = "hero";
  document.startViewTransition(() => {
    // Native browser transition
  });
}
```

### view-transition-name is required (HIGH) (`transition-name-required`)

Without a `view-transition-name`, the browser only does a full-page
crossfade. Elements that should morph between states need a name on both
the source (before the DOM change) and the target (after).

**Incorrect (no transition name):**

```ts
document.startViewTransition(() => {
  targetImg.src = newSrc;
});
```

**Correct (transition name assigned):**

```ts
sourceImg.style.viewTransitionName = "card";
document.startViewTransition(() => {
  sourceImg.style.viewTransitionName = "";
  targetImg.style.viewTransitionName = "card";
});
```

### Names must be unique during the transition (HIGH) (`transition-name-unique`)

Two elements sharing a `view-transition-name` at the same time breaks
the transition — the browser cannot pair them. In lists, derive the name
from the item id.

**Incorrect (duplicate names):**

```css
.card {
  view-transition-name: card;
}
/* Multiple cards with same name */
```

**Correct (unique per element):**

```ts
element.style.viewTransitionName = `card-${id}`;
```

### Clean up names after completion (MEDIUM) (`transition-name-cleanup`)

A stale `view-transition-name` left on the source element collides with
the next transition that reuses the name. Clear the source's name inside
the `startViewTransition` callback when handing it to the target.

**Incorrect (stale name):**

```ts
sourceImg.style.viewTransitionName = "card";
document.startViewTransition(() => {
  targetImg.style.viewTransitionName = "card";
});
```

**Correct (name cleaned up):**

```ts
sourceImg.style.viewTransitionName = "card";
document.startViewTransition(() => {
  sourceImg.style.viewTransitionName = "";
  targetImg.style.viewTransitionName = "card";
});
```

### Style the transition pseudo-elements (MEDIUM) (`transition-style-pseudo-elements`)

The default is a generic crossfade. Customize duration and easing via
the `::view-transition-group()` (and `::view-transition-old()`/
`::view-transition-new()`) pseudo-elements.

**Incorrect (default crossfade only):**

```ts
document.startViewTransition(() => { /* ... */ });
```

**Correct (custom animation):**

```css
::view-transition-group(card) {
  animation-duration: 300ms;
  animation-timing-function: cubic-bezier(0.215, 0.61, 0.355, 1);
}
```
