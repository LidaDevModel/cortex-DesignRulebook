# UI Patterns

Design Fleet's motion and implementation-pattern skill, in two layers.
**Layer 1** is the decision layer — pick easing, timing, and animation
architecture in a few seconds. **Layer 2** indexes the implementation
pattern catalog: 37 rules across 5 categories, each with incorrect/correct
code examples. Detailed tables, cubic-bezier values, spring configs, and
full code patterns live in [references/](references/) and load only when
needed.

**Not this skill:** If the user is building full UI (not just motion or a
specific pattern), use `frontend-design` — it covers motion at a high
level. For visual or behavioral critique of an existing animation or
pattern implementation, use `design-review`. For verifying code against
design rules or a project contract, use `design-review`.

## Layer 1 — Motion Decisions

### Quick Start

Every animation decision starts with these questions:

1. **Is this element entering or exiting?** → Use `ease-out`
2. **Is an on-screen element moving?** → Use `ease-in-out`
3. **Is this a hover/color transition?** → Use `ease`
4. **Will users see this 100+ times daily?** → Don't animate it

### Motion Type Decision Framework

| Motion type | Best choice | Why |
|-------------|-------------|-----|
| User-driven (drag, flick, gesture) | Spring | Survives interruption, preserves velocity |
| System-driven (state change, feedback) | Easing | Clear start/end, predictable timing |
| Time representation (progress, loading) | Linear | 1:1 relationship between time and progress |
| High-frequency (typing, fast toggles) | None | Animation adds noise, feels slower |

### Easing Summary

- **ease-out** is your default for entrances and exits
- **ease-in-out** for elements moving on-screen
- **ease** for hovers and color changes
- **linear** only for marquees and time-as-progress visualizations
- **ease-in** almost never — it delays visual feedback

For the full cubic-bezier values (six strengths each for ease-out and
ease-in-out) and the paired-elements rule, see
[references/easing-curves.md](references/easing-curves.md).

### Timing Summary

| Element type | Duration |
| --- | --- |
| Micro-interactions (press, hover) | 120-180ms |
| Standard UI (tooltips, dropdowns) | 150-250ms |
| Small state changes | 180-260ms |
| Modals, drawers | 200-300ms |

**Project override:** if the project has a VISION.md with an
animation-timing table, that table overrides the generic duration
guidance in this skill.

UI animations stay under 300ms. Larger elements animate slower than
small ones; exits run ~20% faster than entrances. If something feels
slow, **shorten the duration first** before touching the curve.

The frequency rule overrides everything else: if users see this
animation 100+ times a day, drop it or drastically reduce it. Detail
and rationale in
[references/timing-and-duration.md](references/timing-and-duration.md).

### When to Animate

**Do animate:**

- Enter/exit transitions for spatial consistency
- State changes that benefit from visual continuity
- Responses to user actions (feedback)
- Rarely-used interactions where delight adds value

**Don't animate:**

- Keyboard-initiated actions (arrow keys, shortcuts, tab/focus)
- Hover effects on frequently-used elements
- Anything users interact with 100+ times daily
- When speed matters more than smoothness
- Context menu entrances (exit only — context menus should appear instantly)

**Marketing vs. product:**

- Marketing: more elaborate, longer durations allowed
- Product: fast, purposeful, never frivolous

### Animation Principles

**Active state feedback.** Interactive elements must have `:active`
scale transforms for press feedback:

```css
.button:active { transform: scale(0.97); }
```

**Subtle deformation.** Squash/stretch must stay in the 0.95-1.05
range. Anything beyond looks cartoonish in UI.

```tsx
<motion.div whileTap={{ scale: 0.98 }} />
```

**Stagger limits.** Stagger delays must not exceed 50ms per item.
Excessive stagger makes lists feel slow.

```tsx
transition={{ staggerChildren: 0.03 }}
```

**Staging.**

- **One focal point** — only one element should animate prominently at a time
- **Dim backgrounds** — modal/dialog backgrounds should dim to direct focus
- **Z-index hierarchy** — animated elements must respect z-index layers

### Springs

Use springs when you need physics: gestures (drag, flick, swipe),
interruptible motion that must preserve velocity, or "alive" elements
(Dynamic Island). Skip bounce for most UI; reserve it for drag-to-dismiss
and intentionally playful surfaces.

Default config:

```js
{ type: "spring", duration: 0.5, bounce: 0.2 }
```

Full configuration options, the velocity passthrough pattern, and the
quick-reference table by interaction type live in
[references/spring-animations.md](references/spring-animations.md).

### Exit Animations

Framer Motion exits have specific requirements: wrap in
`<AnimatePresence>`, give every conditional element an `exit` prop,
key dynamic lists by stable id (never index), and mirror `initial` for
symmetry.

```tsx
<AnimatePresence>
  {isVisible && (
    <motion.div
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: 20 }}
    />
  )}
</AnimatePresence>
```

Presence hooks, mode selection (`wait` vs `sync` vs `popLayout`), and
nested AnimatePresence rules are in
[references/exit-animations.md](references/exit-animations.md).

### Code Architecture (Storyboard Pattern)

Multi-step sequences should follow the storyboard pattern: an ASCII
shot list at the top, a single `TIMING` object as the only home for
ms values, named element config objects, and a single `stage` integer
that drives the sequence (no scattered booleans).

This keeps choreography editable without rewriting JSX. Full pattern,
the JSX templates, and the shipping checklist are in
[references/storyboard-pattern.md](references/storyboard-pattern.md).

### Performance

The golden rule: only animate `transform` and `opacity`. They skip
layout and paint and run on the GPU. Avoid animating `padding`,
`margin`, `width`, `height`, or blur filters above 20px.

Optimization techniques (`will-change`, animating outside React's
render cycle, hardware-accelerated Framer Motion patterns) are in
[references/performance.md](references/performance.md).

### Accessibility

Every animated element needs a `prefers-reduced-motion` opt-out.
Disable transitions and animations entirely under reduced motion —
not "less of them," none of them. Gate hover animations behind
`@media (hover: hover) and (pointer: fine)` so touch devices don't
trigger false hovers on tap.

```css
@media (prefers-reduced-motion: reduce) {
  .animated { animation: none; transition: none; }
}
```

Framer Motion's `useReducedMotion` hook and the touch-device
implementation are in
[references/accessibility.md](references/accessibility.md).

### Practical Tips

Quick reference for common scenarios. See
[references/practical-tips.md](references/practical-tips.md) for
detailed implementations.

| Scenario | Solution |
| --- | --- |
| Make buttons feel responsive | Add `transform: scale(0.97)` on `:active` |
| Element appears from nowhere | Start from `scale(0.95)`, not `scale(0)` |
| Shaky/jittery animations | Add `will-change: transform` |
| Hover causes flicker | Animate child element, not parent |
| Popover scales from wrong point | Set `transform-origin` to trigger location |
| Sequential tooltips feel slow | Skip delay/animation after first tooltip |
| Small buttons hard to tap | Use 44px minimum hit area (pseudo-element) |
| Something still feels off | Add subtle blur (under 20px) to mask it |
| Hover triggers on mobile | Use `@media (hover: hover) and (pointer: fine)` |
| Animation feels slow | Shorten duration first, then adjust curve |

### Easing Decision Flowchart

Is the element entering or exiting the viewport?
- Yes → ease-out
- No
  - Is it moving/morphing on screen? → ease-in-out
  - Is it a hover change? → ease
  - Is it constant motion? → linear
  - Default → ease-out

## Layer 2 — Implementation Patterns

37 rules across 5 categories. Each category's reference file contains
the per-rule why-it-matters plus incorrect/correct code examples — read
it before implementing the technique.

### 1. CSS Pseudo-Elements (MEDIUM)

Leveraging `::before`, `::after`, and native pseudo-elements to reduce
DOM nodes. Full rules:
[references/pseudo-elements.md](references/pseudo-elements.md)

| Rule | Summary |
|------|---------|
| `pseudo-content-required` | `::before`/`::after` require `content` property to render |
| `pseudo-over-dom-node` | Use pseudo-elements for decoration instead of extra DOM nodes |
| `pseudo-position-relative-parent` | Parent needs `position: relative` for absolute pseudo-elements |
| `pseudo-z-index-layering` | Z-index for correct pseudo-element layering |
| `pseudo-hit-target-expansion` | Negative inset values expand hit targets without extra markup |
| `pseudo-marker-styling` | Use `::marker` for custom list bullet styles |
| `pseudo-first-line-styling` | Use `::first-line` for typographic treatments |
| `native-backdrop-styling` | Use `::backdrop` for dialog/popover backgrounds |
| `native-placeholder-styling` | Use `::placeholder` for input styling |
| `native-selection-styling` | Use `::selection` for text selection styling |

### 2. View Transitions (MEDIUM)

Native browser transitions between DOM states — page navigations,
lightboxes, shared elements. Full rules:
[references/view-transitions.md](references/view-transitions.md)

| Rule | Summary |
|------|---------|
| `transition-over-js-library` | Prefer View Transitions API over JS libraries for page transitions |
| `transition-name-required` | View transitions need `view-transition-name` |
| `transition-name-unique` | Each transition name unique during transition |
| `transition-name-cleanup` | Remove transition name after completion |
| `transition-style-pseudo-elements` | Style `::view-transition-group` for custom animations |

### 3. Morphing Icons (LOW)

Building icon components that morph between any two icons through SVG
line transformation. All icons share a 3-line structure. Full rules:
[references/morphing-icons.md](references/morphing-icons.md)

| Rule | Summary |
|------|---------|
| `morphing-three-lines` | Every icon uses exactly 3 SVG lines |
| `morphing-use-collapsed` | Unused lines use collapsed constant (`{ x1: CENTER, y1: CENTER, x2: CENTER, y2: CENTER, opacity: 0 }`) |
| `morphing-consistent-viewbox` | All icons share same viewBox (14x14) |
| `morphing-group-variants` | Rotational variants share group and base lines |
| `morphing-spring-rotation` | Spring physics for grouped icon rotation |
| `morphing-reduced-motion` | Respect `prefers-reduced-motion` |
| `morphing-jump-non-grouped` | Instant rotation jump between non-grouped icons |
| `morphing-strokelinecap-round` | Round stroke line caps |
| `morphing-aria-hidden` | Icon SVGs are `aria-hidden` |

### 4. Container Animation (MEDIUM)

Animating container width and height using a measure-and-animate pattern
with ResizeObserver and Motion. Full rules:
[references/container-animation.md](references/container-animation.md)

| Rule | Summary |
|------|---------|
| `container-two-div-pattern` | Outer animated div, inner measured div; never same element |
| `container-guard-initial-zero` | Guard bounds === 0 on initial render, fall back to `"auto"` |
| `container-use-resize-observer` | Use ResizeObserver for measurement, not `getBoundingClientRect` |
| `container-overflow-hidden` | Set `overflow: hidden` on animated container during transitions |
| `container-no-excessive-use` | Use sparingly: buttons, accordions, interactive elements |
| `container-callback-ref` | Use callback ref (not `useRef`) for measurement hooks |
| `container-transition-delay` | Add small delay for natural catching-up feel |

### 5. Predictive Prefetching (MEDIUM)

Loading content before the user clicks by analyzing cursor trajectory.
Full rules:
[references/predictive-prefetch.md](references/predictive-prefetch.md)

| Rule | Summary |
|------|---------|
| `prefetch-trajectory-over-hover` | Trajectory prediction over hover; reclaims 100-200ms |
| `prefetch-not-everything` | Prefetch by intent, not viewport; avoid wasted bandwidth |
| `prefetch-hit-slop` | Use hitSlop to trigger predictions earlier |
| `prefetch-touch-fallback` | Fall back gracefully on touch devices (no cursor) |
| `prefetch-keyboard-tab` | Prefetch on keyboard navigation when focus approaches |
| `prefetch-use-selectively` | Use predictive prefetching where latency is noticeable |

## Reference Files

Motion decisions (Layer 1):

- [easing-curves.md](references/easing-curves.md) — full cubic-bezier
  tables for every easing family, paired-elements rule
- [timing-and-duration.md](references/timing-and-duration.md) —
  duration table by element type, frequency rule
- [spring-animations.md](references/spring-animations.md) — when
  springs are required, configuration, quick reference by interaction
- [exit-animations.md](references/exit-animations.md) —
  AnimatePresence rules, presence hooks, mode selection, nesting
- [storyboard-pattern.md](references/storyboard-pattern.md) — ASCII
  storyboard, TIMING object, stage-driven sequencing, JSX templates
- [performance.md](references/performance.md) — golden rule,
  optimization techniques, CSS vs JS tradeoffs
- [accessibility.md](references/accessibility.md) — reduced-motion
  patterns, Framer Motion hook, touch device gating
- [practical-tips.md](references/practical-tips.md) — detailed
  implementations for the scenarios in the table above

Implementation patterns (Layer 2):

- [pseudo-elements.md](references/pseudo-elements.md) — pseudo-element
  mechanics, DOM node replacement, native pseudo-elements
- [view-transitions.md](references/view-transitions.md) — View
  Transitions API naming, cleanup, and custom animation
- [morphing-icons.md](references/morphing-icons.md) — 3-line icon
  system, rotation groups, accessibility
- [container-animation.md](references/container-animation.md) —
  two-div measure-and-animate pattern, guards, restraint
- [predictive-prefetch.md](references/predictive-prefetch.md) —
  trajectory prediction, hitSlop, device fallbacks

External references: [Motion Documentation](https://motion.dev),
[Motion AnimatePresence](https://motion.dev/docs/react-animate-presence),
[Apple WWDC23: Animate with Springs](https://developer.apple.com/videos/play/wwdc2023/10158),
[MDN Pseudo-elements](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/Pseudo-elements),
[View Transitions API](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API),
[ResizeObserver](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver),
[ForesightJS](https://foresightjs.com)
