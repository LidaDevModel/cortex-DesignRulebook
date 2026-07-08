# Storyboard Pattern

Structure animated components for readability and tunability. Every
timing value, scale, position, and spring config is extracted to named
constants so you can edit the choreography without rewriting JSX.

---

## 1. ASCII Storyboard Comment

A block comment at the top of the file that reads like a shot list:

```
/* ─────────────────────────────────────────────────────────
 * ANIMATION STORYBOARD
 *
 *    0ms   waiting for trigger (scroll into view / mount)
 *  300ms   card fades in, scale 0.85 → 1.0
 *  900ms   heading text highlights
 * 1500ms   detail rows slide up (staggered 200ms)
 * 2100ms   CTA button fades in
 * ───────────────────────────────────────────────────────── */
```

Rules: right-align ms values, use `→` for value transitions, note
stagger intervals in parentheses.

## 2. TIMING Object

A single `const TIMING` object with every stage delay in milliseconds.
This is the **only place** timing values live.

```tsx
const TIMING = {
  cardAppear:    300,
  headingGlow:   900,
  detailRows:    1500,
  ctaButton:     2100,
};
```

Keys are camelCase descriptive verb phrases. Values are ms after the
animation trigger (not deltas between stages).

## 3. Element Config Objects

Each animated element gets its own named config object:

```tsx
const CARD = {
  initialScale: 0.85,
  finalScale:   1.0,
  spring: { type: "spring" as const, stiffness: 300, damping: 30 },
};

const ROWS = {
  stagger:  0.2,
  offsetY:  12,
  spring: { type: "spring" as const, stiffness: 300, damping: 30 },
  items: [
    { label: "Row 1", value: "A" },
    { label: "Row 2", value: "B" },
  ],
};
```

UPPERCASE names. Group ALL values for one element together. Repeated
items are arrays rendered with `.map()`.

## 4. Stage-Driven Sequencing

A single `stage` integer state drives the entire sequence — no scattered
boolean flags:

```tsx
const [stage, setStage] = useState(0);

useEffect(() => {
  if (!isInView) { setStage(0); return; }

  setStage(0);
  const timers: NodeJS.Timeout[] = [];

  timers.push(setTimeout(() => setStage(1), TIMING.cardAppear));
  timers.push(setTimeout(() => setStage(2), TIMING.headingGlow));
  timers.push(setTimeout(() => setStage(3), TIMING.detailRows));
  timers.push(setTimeout(() => setStage(4), TIMING.ctaButton));

  return () => timers.forEach(clearTimeout);
}, [isInView, replayTrigger]);
```

## 5. JSX Pattern

Reference config objects in animate props, use `stage >= N` checks:

```tsx
<motion.div
  initial={{ opacity: 0, scale: CARD.initialScale }}
  animate={{
    opacity: stage >= 1 ? 1 : 0,
    scale:   stage >= 1 ? CARD.finalScale : CARD.initialScale,
  }}
  transition={CARD.spring}
/>
```

For staggered groups:

```tsx
{ROWS.items.map((item, i) => (
  <motion.div
    key={item.label}
    initial={{ opacity: 0, y: ROWS.offsetY }}
    animate={{
      opacity: stage >= 3 ? 1 : 0,
      y:       stage >= 3 ? 0 : ROWS.offsetY,
    }}
    transition={{ ...ROWS.spring, delay: i * ROWS.stagger }}
  >
    {item.label}
  </motion.div>
))}
```

## Storyboard Checklist

- [ ] ASCII storyboard comment at top matches actual TIMING values
- [ ] Zero magic numbers in JSX or useEffect — everything references a const
- [ ] Springs defined in config objects, not inline
- [ ] Repeated elements use `.map()` over a data array
- [ ] Stage values use `>=` checks (stages are additive)
- [ ] `replayTrigger` in dependency array for dev/debug replay
