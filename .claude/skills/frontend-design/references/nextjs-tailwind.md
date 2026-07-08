# Next.js + Tailwind Reference

Stack-specific guidance for projects using Next.js and Tailwind CSS. Adapt these patterns when using other frameworks.

## Font Loading with next/font

**Load fonts via `next/font`** — never from raw `<link>` tags or `@import` in CSS. For fonts not available through the Google Fonts API (e.g. Google Sans), download the files and use `next/font/local`:

```tsx
import localFont from 'next/font/local';

const googleSans = localFont({
  src: [
    { path: '../fonts/GoogleSans-Regular.ttf', weight: '400', style: 'normal' },
    { path: '../fonts/GoogleSans-Medium.ttf', weight: '500', style: 'normal' },
    { path: '../fonts/GoogleSans-Bold.ttf', weight: '700', style: 'normal' },
  ],
  variable: '--font-google-sans',
  display: 'swap',
});
```

### CSS Variable Scoping (Tailwind v4)

The font variable class is applied on `<body>` via `className`. In Tailwind v4, `@theme inline` maps theme tokens to CSS variables. If you apply `font-sans` on `html`, it cannot read a variable set on `body` (CSS variables don't flow upward). Always apply `font-sans` on `body`, not `html`:

```css
/* globals.css — WRONG */
html { @apply font-sans; }

/* globals.css — CORRECT */
body { @apply font-sans; }
```

In the `@theme inline` block, use a distinct variable name to avoid circular references:

```css
@theme inline {
  --font-sans: var(--font-google-sans);
}
```

## Tailwind Color Tokens

**Define CSS variables in `globals.css`**, wire them through `tailwind.config.js`, and use the resulting Tailwind utilities:

```css
/* globals.css */
:root {
  --color-surface: #0a0a0a;
  --color-primary: #e4ff1a;
  --color-muted: #71717a;
}
```

```js
/* tailwind.config.js */
colors: {
  surface: 'var(--color-surface)',
  primary: 'var(--color-primary)',
  muted: 'var(--color-muted)',
}
```

Then use `bg-surface`, `text-primary`, `border-muted` etc. in components. Never use raw `var()` in inline styles when a Tailwind utility can express it.

## Styling: Tailwind + cn()

All styling goes through Tailwind utilities. Use the project's `cn()` helper (from shadcn/ui or similar) for conditional classes:

```tsx
import { cn } from '@/lib/utils';

export function Card({ variant, className, ...props }: CardProps) {
  return (
    <div
      className={cn(
        'rounded-2xl border border-white/10 backdrop-blur-sm',
        variant === 'elevated' && 'shadow-2xl shadow-primary/20',
        variant === 'flat' && 'bg-surface/50',
        className
      )}
      {...props}
    />
  );
}
```

Reach for raw CSS (in `globals.css`) only when Tailwind cannot express the effect — complex gradients, `@keyframes`, noise SVG filters, or pseudo-element tricks. Keep raw CSS minimal and co-located in globals, not scattered in component files.

### Avoid Inline Styles

```tsx
// BAD
<div style={{ background: 'var(--color-surface)', marginTop: 20 }}>

// GOOD
<div className="bg-surface mt-5">
```

## React Server Components

Components are server components by default. Only add `'use client'` for components that need interactivity (hooks, event handlers, browser APIs).

Push `'use client'` to the **lowest leaf** that actually needs it. A page with a hero animation should look like:

```
page.tsx           → server component (fetches data, renders layout)
  HeroSection.tsx  → server component (static markup + Tailwind animations)
  HeroActions.tsx  → 'use client' (interactive buttons, hover effects needing JS)
```

If an effect can be achieved with pure CSS (`transition`, `animation`, `@keyframes`, `:hover`), keep the component as a server component.

## Anti-Patterns (Next.js / Tailwind specific)

- Inline `style={{ }}` for things Tailwind can handle
- `'use client'` on an entire page when only a button needs interactivity
- Raw `var()` in inline styles instead of Tailwind utilities wired to CSS variables
