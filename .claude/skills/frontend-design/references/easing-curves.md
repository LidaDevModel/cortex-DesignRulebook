# Easing Curves

Detailed reference for the cubic-bezier values that power the
`frontend-design` skill. SKILL.md covers when to pick each
family — this file is the lookup table when you need a specific
value or want to understand the curve shape.

---

## ease-out (most common)

Use for **user-initiated interactions**: dropdowns, modals, tooltips,
any element entering or exiting the screen.

```css
/* Sorted weak to strong */
--ease-out-quad:  cubic-bezier(0.25, 0.46, 0.45, 0.94);
--ease-out-cubic: cubic-bezier(0.215, 0.61, 0.355, 1);
--ease-out-quart: cubic-bezier(0.165, 0.84, 0.44, 1);
--ease-out-quint: cubic-bezier(0.23, 1, 0.32, 1);
--ease-out-expo:  cubic-bezier(0.19, 1, 0.22, 1);
--ease-out-circ:  cubic-bezier(0.075, 0.82, 0.165, 1);
```

Why it works: acceleration at the start creates an instant, responsive
feeling. The element "jumps" toward its destination then settles in.

## ease-in-out (for movement)

Use when **elements already on screen need to move or morph**. Mimics
natural motion like a car accelerating then braking.

```css
/* Sorted weak to strong */
--ease-in-out-quad:  cubic-bezier(0.455, 0.03, 0.515, 0.955);
--ease-in-out-cubic: cubic-bezier(0.645, 0.045, 0.355, 1);
--ease-in-out-quart: cubic-bezier(0.77, 0, 0.175, 1);
--ease-in-out-quint: cubic-bezier(0.86, 0, 0.07, 1);
--ease-in-out-expo:  cubic-bezier(1, 0, 0, 1);
--ease-in-out-circ:  cubic-bezier(0.785, 0.135, 0.15, 0.86);
```

## ease (for hover effects)

Use for **hover states and color transitions**. The asymmetrical curve
(faster start, slower end) feels elegant for gentle animations.

```css
transition: background-color 150ms ease;
```

## linear (avoid in UI)

Only use for:

- Constant-speed animations (marquees, tickers)
- Time visualization (hold-to-delete progress indicators)

Linear feels robotic and unnatural for interactive elements.

## ease-in (almost never)

**Avoid for UI animations.** Makes interfaces feel sluggish because the
slow start delays visual feedback.

## Paired Elements Rule

> Delta: baseline models miss this — paired elements (dropdown + backdrop) share easing AND duration.

Elements that animate together must use the same easing and duration.
Modal + overlay, tooltip + arrow, drawer + backdrop — if they move as
a unit, they should feel like a unit.

```css
.modal {
  transition: transform 200ms ease-out;
}
.overlay {
  transition: opacity 200ms ease-out;
}
```
