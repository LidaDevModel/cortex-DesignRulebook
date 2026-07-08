# Morphing Icons

A single icon component that morphs between any two icons by animating
SVG lines. The trick: every icon is defined as exactly three lines in a
shared coordinate space, so Motion can interpolate line endpoints
directly — no path morphing library needed. This file is the complete
contract: structure, rotation behavior, and accessibility.

---

## Structure: Three Lines, One ViewBox

### Every icon uses exactly 3 lines (HIGH) (`morphing-three-lines`)

The morph interpolates line N of the old icon to line N of the new icon.
That only works if every icon has the same number of lines — exactly
three, no more, no fewer. Icons that need fewer visible lines pad with
the collapsed constant.

**Incorrect (only 2 lines):**

```ts
const checkIcon = {
  lines: [
    { x1: 2, y1: 7.5, x2: 5.5, y2: 11 },
    { x1: 5.5, y1: 11, x2: 12, y2: 3 },
  ],
};
```

**Correct (3 lines with collapsed):**

```ts
const checkIcon = {
  lines: [
    { x1: 2, y1: 7.5, x2: 5.5, y2: 11 },
    { x1: 5.5, y1: 11, x2: 12, y2: 3 },
    collapsed,
  ],
};
```

### Unused lines use the collapsed constant (HIGH) (`morphing-use-collapsed`)

A collapsed line is a zero-length, invisible line at the icon's center:

```ts
const collapsed = { x1: CENTER, y1: CENTER, x2: CENTER, y2: CENTER, opacity: 0 };
```

Never use `null` or omit the line — the interpolator needs real
coordinates to animate from and to, and a collapsed line shrinks into
(or grows out of) the center gracefully.

**Incorrect (null for unused):**

```ts
const minusIcon = {
  lines: [
    { x1: 2, y1: 7, x2: 12, y2: 7 },
    null,
    null,
  ],
};
```

**Correct (collapsed constant):**

```ts
const minusIcon = {
  lines: [
    { x1: 2, y1: 7, x2: 12, y2: 7 },
    collapsed,
    collapsed,
  ],
};
```

### All icons share the same viewBox (HIGH) (`morphing-consistent-viewbox`)

Line coordinates only interpolate sensibly when every icon lives in the
same coordinate space. Use one viewBox (14x14 recommended) and one
center constant for the whole icon set.

**Incorrect (mixed scales):**

```ts
const icon1 = { lines: [{ x1: 2, y1: 7, x2: 12, y2: 7 }, ...] }; // 14x14
const icon2 = { lines: [{ x1: 4, y1: 14, x2: 24, y2: 14 }, ...] }; // 28x28
```

**Correct (consistent scale):**

```ts
const VIEWBOX_SIZE = 14;
const CENTER = 7;
```

## Rotation: Groups and Springs

### Rotational variants share a group and base lines (HIGH) (`morphing-group-variants`)

Icons that are rotations of each other (arrow-up/down/left/right,
chevrons) must share one set of base lines plus a `rotation` and a
`group` tag. The morph then becomes a pure rotation instead of four
unrelated line interpolations.

**Incorrect (different line definitions):**

```ts
const arrowRight = { lines: [{ x1: 2, y1: 7, x2: 12, y2: 7 }, ...] };
const arrowDown = { lines: [{ x1: 7, y1: 2, x2: 7, y2: 12 }, ...] };
```

**Correct (shared base lines):**

```ts
const arrowLines: [IconLine, IconLine, IconLine] = [
  { x1: 2, y1: 7, x2: 12, y2: 7 },
  { x1: 7.5, y1: 2.5, x2: 12, y2: 7 },
  { x1: 7.5, y1: 11.5, x2: 12, y2: 7 },
];

const icons = {
  "arrow-right": { lines: arrowLines, rotation: 0, group: "arrow" },
  "arrow-down": { lines: arrowLines, rotation: 90, group: "arrow" },
  "arrow-left": { lines: arrowLines, rotation: 180, group: "arrow" },
  "arrow-up": { lines: arrowLines, rotation: -90, group: "arrow" },
};
```

### Spring physics for grouped rotation (MEDIUM) (`morphing-spring-rotation`)

Rotation between icons in the same group should use a spring, not a
fixed duration — it survives interruption (rapid toggling) and settles
naturally.

**Incorrect (duration-based rotation):**

```tsx
<motion.g animate={{ rotate: rotation }} transition={{ duration: 0.3 }} />
```

**Correct (spring rotation):**

```tsx
const rotation = useSpring(definition.rotation ?? 0, activeTransition);

<motion.g style={{ rotate: rotation, transformOrigin: "center" }} />
```

### Instant jump between non-grouped icons (MEDIUM) (`morphing-jump-non-grouped`)

When the next icon is NOT in the same group, animating the rotation
produces a meaningless spin while the lines morph. Jump the rotation
instantly and let the line morph carry the transition.

**Incorrect (always animates rotation):**

```tsx
useEffect(() => {
  rotation.set(definition.rotation ?? 0);
}, [definition]);
```

**Correct (jumps when not grouped):**

```tsx
useEffect(() => {
  if (shouldRotate) {
    rotation.set(definition.rotation ?? 0);
  } else {
    rotation.jump(definition.rotation ?? 0);
  }
}, [definition, shouldRotate]);
```

## Polish and Accessibility

### Round stroke line caps (LOW) (`morphing-strokelinecap-round`)

Collapsing and growing lines look unfinished with flat ends. Round caps
keep endpoints polished at every interpolation frame.

**Incorrect (butt caps):**

```tsx
<motion.line strokeLinecap="butt" />
```

**Correct (round caps):**

```tsx
<motion.line strokeLinecap="round" />
```

### Respect prefers-reduced-motion (MEDIUM) (`morphing-reduced-motion`)

Morphing is exactly the kind of motion reduced-motion users opt out of.
Collapse the transition to zero duration — the icon still changes, it
just doesn't animate.

**Incorrect (always animates):**

```tsx
function MorphingIcon({ icon }: Props) {
  return <motion.line animate={...} transition={{ duration: 0.4 }} />;
}
```

**Correct (respects preference):**

```tsx
function MorphingIcon({ icon }: Props) {
  const reducedMotion = useReducedMotion() ?? false;
  const activeTransition = reducedMotion ? { duration: 0 } : transition;

  return <motion.line animate={...} transition={activeTransition} />;
}
```

### Icon SVGs are aria-hidden (LOW) (`morphing-aria-hidden`)

The icon is decorative; the button or link it sits in carries the
accessible name. Hide the SVG from assistive tech.

**Incorrect (no aria attribute):**

```tsx
<svg width={size} height={size}>...</svg>
```

**Correct (aria-hidden):**

```tsx
<svg width={size} height={size} aria-hidden="true">...</svg>
```
