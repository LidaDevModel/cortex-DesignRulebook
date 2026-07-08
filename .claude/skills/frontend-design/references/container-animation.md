# Container Animation

Animating a container's width or height as its content changes —
loading-state buttons, accordions, expandable sections. The pattern is
measure-and-animate: one element reports its natural size, another
animates to match it. Get the separation wrong and you get feedback
loops; skip the guards and you get a flash from zero on mount.

> **Delta knowledge** — Baseline models reliably miss two lifecycle
> details here: using a callback ref (not `useRef`) so the ResizeObserver
> attaches exactly when the node mounts, and guarding the initial
> zero-bounds measurement with a fallback to `'auto'` (AUDIT.md,
> ui-patterns#6). This reference is load-bearing: it is part of the
> package's knowledge-injection core.

---

### The Two-Div Pattern (HIGH) (`container-two-div-pattern`)

Use an outer animated div and an inner measured div. Never measure and
animate the same element — animating the height changes the measurement,
which re-triggers the animation, creating a feedback loop.

**Incorrect (measure and animate same element — creates feedback loop):**

```tsx
function AnimatedContainer({ children }) {
  const [ref, bounds] = useMeasure();
  return (
    <motion.div ref={ref} animate={{ height: bounds.height }}>
      {children}
    </motion.div>
  );
}
```

**Correct (separate measure and animate targets):**

```tsx
function AnimatedContainer({ children }) {
  const [ref, bounds] = useMeasure();
  return (
    <motion.div animate={{ height: bounds.height }}>
      <div ref={ref}>{children}</div>
    </motion.div>
  );
}
```

### Guard Against Zero on Initial Render (HIGH) (`container-guard-initial-zero`)

On first render the measured bounds are 0. Without a guard the container
animates from 0 to its actual size on mount — a visible pop-open. Fall
back to `"auto"` until the measurement is positive.

**Incorrect (animates from 0 on mount):**

```tsx
<motion.div animate={{ width: bounds.width }}>
  <div ref={ref}>{children}</div>
</motion.div>
```

**Correct (falls back to auto on first frame):**

```tsx
<motion.div animate={{ width: bounds.width > 0 ? bounds.width : "auto" }}>
  <div ref={ref}>{children}</div>
</motion.div>
```

### Measure with ResizeObserver (MEDIUM) (`container-use-resize-observer`)

Use ResizeObserver to track element dimensions. It fires only when the
size actually changes, without the layout thrashing of calling
`getBoundingClientRect` on every render.

**Incorrect (measuring on every render):**

```tsx
function useMeasure(ref) {
  const [bounds, setBounds] = useState({ width: 0, height: 0 });
  useEffect(() => {
    if (ref.current) {
      const rect = ref.current.getBoundingClientRect();
      setBounds({ width: rect.width, height: rect.height });
    }
  });
  return bounds;
}
```

**Correct (ResizeObserver):**

```tsx
function useMeasure() {
  const [element, setElement] = useState(null);
  const [bounds, setBounds] = useState({ width: 0, height: 0 });
  const ref = useCallback((node) => setElement(node), []);

  useEffect(() => {
    if (!element) return;
    const observer = new ResizeObserver(([entry]) => {
      setBounds({
        width: entry.contentRect.width,
        height: entry.contentRect.height,
      });
    });
    observer.observe(element);
    return () => observer.disconnect();
  }, [element]);

  return [ref, bounds];
}
```

### Use a Callback Ref, Not useRef (MEDIUM) (`container-callback-ref`)

A measurement hook built on `useRef` can miss the node: `ref.current`
may be null when the effect first runs, and the effect never re-fires
when the node attaches. A callback ref puts the node in state, so the
observer effect runs exactly when the node is ready.

**Incorrect (useRef may be null on first effect):**

```tsx
const ref = useRef(null);
useEffect(() => {
  if (!ref.current) return;
  observer.observe(ref.current);
}, []);
```

**Correct (callback ref guarantees node):**

```tsx
const [element, setElement] = useState(null);
const ref = useCallback((node) => setElement(node), []);
useEffect(() => {
  if (!element) return;
  observer.observe(element);
  return () => observer.disconnect();
}, [element]);
```

### Overflow Hidden During Transitions (MEDIUM) (`container-overflow-hidden`)

While the outer container is mid-animation it is smaller (or larger)
than its content. Set `overflow: hidden` on the animated container so
content clips cleanly instead of spilling out.

**Incorrect (content overflows during animation):**

```tsx
<motion.div animate={{ height: bounds.height }}>
  <div ref={ref}>{children}</div>
</motion.div>
```

**Correct (clipped during transition):**

```tsx
<motion.div animate={{ height: bounds.height }} style={{ overflow: "hidden" }}>
  <div ref={ref}>{children}</div>
</motion.div>
```

### Add a Small Delay (LOW) (`container-transition-delay`)

A short delay makes the container feel like it's catching up to its
content — the content changes first, the container follows. This reads
as natural rather than mechanical.

**Correct:**

```tsx
<motion.div
  animate={{ height: bounds.height }}
  transition={{ duration: 0.2, delay: 0.05 }}
  style={{ overflow: "hidden" }}
>
  <div ref={ref}>{children}</div>
</motion.div>
```

### Use Sparingly (MEDIUM) (`container-no-excessive-use`)

Animated bounds is a subtle effect. Use it for elements that change size
in response to interaction — not as a default wrapper.

**Good use cases:** loading state buttons, expandable sections,
accordions, FAQs, content reveals.

**Bad use cases:** every container on the page, static layouts, elements
that don't change size.
