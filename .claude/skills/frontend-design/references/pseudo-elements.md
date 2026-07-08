# CSS Pseudo-Elements

Use pseudo-elements to add decoration, expand hit targets, and style
native UI surfaces without adding DOM nodes. Three mechanics rules make
`::before`/`::after` work at all; the rest of this file covers when a
pseudo-element beats a real element and which native pseudo-elements
replace common wrapper hacks.

---

## Core Mechanics

### Content property is required (HIGH) (`pseudo-content-required`)

`::before` and `::after` do not render without a `content` property.
A missing `content: ""` is the most common reason a pseudo-element
silently fails to appear.

**Incorrect (missing content):**

```css
.button::before {
  position: absolute;
  background: var(--gray-3);
}
```

**Correct (content set):**

```css
.button::before {
  content: "";
  position: absolute;
  background: var(--gray-3);
}
```

### Parent needs position: relative (HIGH) (`pseudo-position-relative-parent`)

An absolutely positioned pseudo-element positions itself against the
nearest positioned ancestor. Without `position: relative` on the parent,
it anchors to some ancestor further up the tree and lands in the wrong
place.

**Incorrect (no position on parent):**

```css
.button::before {
  content: "";
  position: absolute;
  inset: 0;
}
```

**Correct (parent positioned):**

```css
.button {
  position: relative;
}

.button::before {
  content: "";
  position: absolute;
  inset: 0;
}
```

### Z-index layering (MEDIUM) (`pseudo-z-index-layering`)

A pseudo-element with a background paints on top of the element's text
unless you layer it explicitly. Give the parent a stacking context and
push the pseudo-element behind with `z-index: -1`.

**Incorrect (covers text):**

```css
.button::before {
  content: "";
  position: absolute;
  inset: 0;
  background: var(--gray-3);
}
```

**Correct (layered behind):**

```css
.button {
  position: relative;
  z-index: 1;
}

.button::before {
  content: "";
  position: absolute;
  inset: 0;
  background: var(--gray-3);
  z-index: -1;
}
```

## Replacing DOM Nodes

### Pseudo-elements over DOM nodes (MEDIUM) (`pseudo-over-dom-node`)

Decorative content belongs in CSS, not markup. Every extra `<span>` or
`<div>` for a background, underline, or glow adds DOM weight and JSX
noise for something purely visual.

**Incorrect (extra DOM node):**

```tsx
<button className={styles.button}>
  <span className={styles.background} />
  Click me
</button>
```

**Correct (pseudo-element):**

```tsx
<button className={styles.button}>
  Click me
</button>
```

```css
.button::before {
  content: "";
  /* decorative background */
}
```

### Hit target expansion (MEDIUM) (`pseudo-hit-target-expansion`)

Small controls are hard to tap. Negative `inset` values on a
pseudo-element expand the clickable area without wrapper elements or
layout changes. (44px is the Apple/WCAG minimum target size.)

**Incorrect (wrapper for hit target):**

```tsx
<div className={styles.wrapper}>
  <a className={styles.link}>Link</a>
</div>
```

**Correct (pseudo-element expansion):**

```css
.link {
  position: relative;
}

.link::before {
  content: "";
  position: absolute;
  inset: -8px -12px;
}
```

## Native Pseudo-Elements

The browser already exposes styling hooks for list bullets, first lines,
dialog backdrops, placeholders, and text selection. Reach for these
before building a custom replacement.

### ::marker for list bullets (LOW) (`pseudo-marker-styling`)

Style list bullets directly instead of hiding them and faking a bullet
with a background image.

**Incorrect (background image hack):**

```css
li {
  list-style: none;
  background: url("bullet.svg") no-repeat 0 4px;
  padding-left: 20px;
}
```

**Correct (native ::marker):**

```css
li::marker {
  color: var(--gray-8);
  font-size: 0.8em;
}
```

### ::first-line for typographic treatments (LOW) (`pseudo-first-line-styling`)

Drop-cap-adjacent styling without JavaScript or hardcoded spans — the
browser recalculates the first line on every reflow for free.

**Incorrect (manual span):**

```tsx
<p>
  <span className={styles["first-line"]}>The opening line of this paragraph</span>
  is styled differently from the rest.
</p>
```

**Correct (native ::first-line):**

```css
.article p:first-of-type::first-line {
  font-variant-caps: small-caps;
  font-weight: var(--font-weight-medium);
}
```

### ::backdrop for dialog backgrounds (MEDIUM) (`native-backdrop-styling`)

Native `<dialog>` and popover elements get a `::backdrop` pseudo-element.
Use it instead of rendering and wiring up a separate overlay node.

**Incorrect (extra overlay node):**

```tsx
<>
  <div className={styles.overlay} onClick={close} />
  <dialog className={styles.dialog}>{children}</dialog>
</>
```

**Correct (native ::backdrop):**

```css
dialog::backdrop {
  background: var(--black-a6);
  backdrop-filter: blur(4px);
}
```

### ::placeholder for input styling (LOW) (`native-placeholder-styling`)

Style the input's own placeholder instead of overlaying a fake one that
must be shown and hidden in sync with the value.

**Incorrect (custom placeholder node):**

```tsx
<div className={styles.inputWrapper}>
  {!value && <span className={styles.placeholder}>Enter text...</span>}
  <input value={value} />
</div>
```

**Correct (native ::placeholder):**

```css
input::placeholder {
  color: var(--gray-9);
  opacity: 1;
}
```

### ::selection for text selection (LOW) (`native-selection-styling`)

Brand the text selection color — one of the few global styling hooks
with zero markup cost.

**Correct:**

```css
::selection {
  background: var(--blue-a5);
  color: var(--gray-12);
}
```
