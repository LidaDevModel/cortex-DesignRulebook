# Exit Animations (AnimatePresence)

Framer Motion exit animations have specific structural requirements.
Get any of them wrong and elements either fail to animate out or
disappear instantly. This file is the checklist.

---

## Core Rules

- `exit-requires-wrapper` — Conditional motion elements must be wrapped
  in `<AnimatePresence>`
- `exit-prop-required` — Elements inside AnimatePresence need an
  `exit` prop
- `exit-key-required` — Dynamic lists need unique keys (`item.id`),
  never array index
- `exit-matches-initial` — Exit animation should mirror initial for
  symmetry

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

## Presence Hooks

- `presence-hook-in-child` — `useIsPresent` must be called from the
  child of AnimatePresence, not the parent
- `presence-safe-to-remove` — Call `safeToRemove` after async cleanup
  when using `usePresence`
- `presence-disable-interactions` — Disable interactions on exiting
  elements via `disabled={!isPresent}`

## Mode Selection

- `mode-wait-doubles-duration` — Mode `"wait"` nearly doubles perceived
  duration; halve your timing values when using it
- `mode-sync-layout-conflict` — Mode `"sync"` causes layout conflicts;
  use `"popLayout"` instead for lists
- `mode-pop-layout-for-lists` — Use `popLayout` mode for list reordering
  to prevent layout shifts

## Nested AnimatePresence

- `nested-propagate-required` — Nested AnimatePresence needs the
  `propagate` prop on both parent and child
- `nested-consistent-timing` — Coordinate parent-child exit durations;
  child must finish before parent
