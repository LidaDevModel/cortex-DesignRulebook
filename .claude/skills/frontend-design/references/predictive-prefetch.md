# Predictive Prefetching

Loading content before the user clicks by predicting intent from cursor
trajectory, expanded hit areas, and keyboard focus — the ForesightJS
model. Done right it makes navigation feel instant; done wrong it wastes
bandwidth on pages nobody visits. The rules below cover both halves.

> **Delta knowledge** — The intent-signal layer here — cursor trajectory
> prediction, `hitSlop` expansion, keyboard-focus approach — is beyond
> baseline knowledge: cold models give only hover, viewport, and
> Speculation-Rules treatments (AUDIT.md, P12 + ui-patterns#7). This
> reference is load-bearing: it is part of the package's
> knowledge-injection core.

---

### Trajectory Prediction Over Hover (HIGH) (`prefetch-trajectory-over-hover`)

Hover prefetching starts too late — the cursor is already on the link.
Trajectory prediction fires while the cursor is still in motion toward
the target, reclaiming 100-200ms of load time.

**Incorrect (waits for hover):**

```tsx
<Link
  href="/about"
  onMouseEnter={() => router.prefetch("/about")}
>
  About
</Link>
```

**Correct (trajectory-based):**

```tsx
const { elementRef } = useForesight({
  callback: () => router.prefetch("/about"),
  hitSlop: 20,
  name: "about-link",
});

<Link ref={elementRef} href="/about">About</Link>
```

### Prefetch by Intent, Not Viewport (HIGH) (`prefetch-not-everything`)

Don't prefetch everything visible in the viewport. Blanket prefetching
wastes bandwidth and server capacity on links the user never follows.
Disable framework auto-prefetch and let intent prediction decide.

**Incorrect (prefetch all visible links):**

```tsx
<Link href="/page" prefetch={true}>Page</Link>
```

**Correct (intent-based prefetching):**

```tsx
<Link href="/page" prefetch={false}>Page</Link>
// Let trajectory/hover prediction handle it
```

### Use hitSlop for Earlier Predictions (MEDIUM) (`prefetch-hit-slop`)

Expand the invisible prediction area around elements with `hitSlop` so
the prediction fires sooner — the trajectory only has to point at the
expanded zone, not the exact element bounds.

**Incorrect (tight prediction area):**

```tsx
const { elementRef } = useForesight({
  callback: () => prefetch(),
  hitSlop: 0,
});
```

**Correct (expanded prediction area):**

```tsx
const { elementRef } = useForesight({
  callback: () => prefetch(),
  hitSlop: 20,
});
```

### Fall Back Gracefully on Touch Devices (MEDIUM) (`prefetch-touch-fallback`)

Touch devices have no cursor, so trajectory prediction has nothing to
work with. Use a strategy that falls back to viewport or touch-start
prefetching automatically instead of hand-rolling mouse-only handlers.

**Incorrect (assumes cursor exists):**

```tsx
function PrefetchLink({ href, children }) {
  return (
    <Link
      href={href}
      onMouseMove={() => prefetch(href)}
    >
      {children}
    </Link>
  );
}
```

**Correct (device-aware strategy):**

```tsx
const { elementRef } = useForesight({
  callback: () => router.prefetch(href),
  hitSlop: 20,
});
// Automatically falls back to touch-start on mobile
```

### Prefetch on Keyboard Navigation (MEDIUM) (`prefetch-keyboard-tab`)

Keyboard users signal intent too. Monitor focus changes and prefetch
when focus is a few tab stops away from a registered element — the
keyboard equivalent of an approaching cursor.

**Correct (tab-aware prefetching):**

```tsx
const { elementRef } = useForesight({
  callback: () => router.prefetch("/settings"),
  name: "settings-link",
  // Tab prediction fires when focus approaches
});
```

### Use Selectively (MEDIUM) (`prefetch-use-selectively`)

Predictive prefetching doesn't belong in every project. Use it where
navigation latency is actually noticeable.

**Good use cases:** data-heavy dashboards, multi-page apps with slow API
responses, e-commerce product pages.

**Bad use cases:** static sites with instant navigation, single-page
apps with all data preloaded.
