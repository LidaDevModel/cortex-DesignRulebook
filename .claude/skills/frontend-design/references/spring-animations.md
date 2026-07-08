# Spring Animations

Springs feel more natural than easing curves because they don't have
fixed durations — they simulate real physics. This file covers when
springs are required, how to configure them, and the failure modes
to avoid.

---

## When to Use Springs

- Gesture-driven motion (drag, flick, swipe) — springs **must** be used
- Interruptible motion — springs **must** be used (they preserve
  velocity when interrupted; CSS animations restart from zero)
- Elements that should feel "alive" (Dynamic Island)
- Organic, playful interfaces
- Any motion needing overshoot-and-settle behavior

## Configuration

**Apple's approach (recommended for most cases):**

```js
{ type: "spring", duration: 0.5, bounce: 0.2 }
```

**Traditional physics (more control):**

```js
{ type: "spring", mass: 1, stiffness: 100, damping: 10 }
```

## Spring Guidelines

- **Avoid bounce** in most UI contexts
- **Use bounce** for drag-to-dismiss, playful interactions
- Keep bounce subtle (0.1-0.3) when used
- Balanced parameters: avoid excessive oscillation
  (e.g. `stiffness: 1000, damping: 5` is too bouncy)
- When velocity matters (drag release), pass input velocity to the
  spring:

```tsx
onDragEnd={(e, info) => {
  animate(target, { x: 0 }, {
    type: "spring",
    velocity: info.velocity.x,
  });
}}
```

## Quick Reference

| Interaction | Timing | Type |
|-------------|--------|------|
| Drag release | Spring | `stiffness: 500, damping: 30` |
| Button press | 150ms | `ease` |
| Modal enter | 200ms | `ease-out` |
| Modal exit | 150ms | `ease-out` |
| Page transition | 250ms | `ease-in-out` |
| Progress bar | varies | `linear` |
| Typing feedback | 0ms | none |
