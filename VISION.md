# VISION — Avante Cortex

<!-- qd-vision-template: v1 -->

> **Read this before every non-trivial change.** This is the hard-rails contract for working in this project. If a rule below conflicts with something you're about to do, **stop and ask** — don't quietly improvise. The goal is one coherent product, not seven flavors of "almost right."

Unfilled values look [LIKE-THIS]. A section full of placeholders is declared not-applicable-yet, not missing — `design-lint` reports it as such instead of failing. Never delete a section to hide an unfilled value.

---

## North Star

Avante Cortex is a proprietary AI-powered knowledge and training platform built exclusively for Avante Security. It gives every person in the organisation — field security guards on shift, back office admins at their desks, new hires in onboarding, and operations managers reviewing analytics — instant access to accurate, role-appropriate knowledge. Mobile-first for field guards (AI chat is their primary use case mid-shift), desktop-optimised for back office and admin roles. The one outcome the product is optimised for: any Avante staff member gets the right answer, in the right format, in the fewest possible steps. Production — not a demo. Every screen must feel like the same hand drew it.

---

## The unbreakable rules (read these first)

**ALWAYS**

1. **Use design tokens.** Every color, font, radius, spacing, and size MUST come from the canonical token map (see **Design tokens**). Inlining a one-off "close enough" value is the single most common drift vector.
2. **Apply the signature visual technique** — the soft green radial glow — to every main canvas surface. See **Signature visual technique**.
3. **Follow the canonical layout pattern** for the surface you're on (see **Layout pattern**). Don't invent new compositions for one screen.
4. **Route errors through the feedback helper** — never inline error text under a field unless the layout pattern explicitly mandates it. See **Errors & validation**.
5. **Communicate with the app shell through the documented protocol** (nav state, modal state, role indicator). Never render your own copy of a global UI element.
6. **Report modal/overlay state** so global UI (nav, breadcrumb) can yield while the overlay owns the viewport.
7. **Use only Plus Jakarta Sans** across the entire product. No system fonts. No additional families.
8. **Match the existing wording style.** Sentence case for buttons and labels. Plain language. No emojis in shipping copy. No "Oops/Whoops/Yay/Awesome."
9. **Respect role boundaries at every layer.** Content, navigation items, and API calls must be gated by the authenticated user's role.

**NEVER**

1. **Never introduce a new color, font, radius, or spacing value** without adding it to the token map first. No siblings of existing tokens.
2. **Never show content outside the user's role.** Role boundary violations are a security and trust issue — not a UX edge case.
3. **Never let a required selection end up empty** through a re-click or background click. The only no-selection path is an explicit "skip" affordance.
4. **Never use inline error text** under a field unless that's the documented pattern. Default is toast/banner + a field-error state class.
5. **Never break the canonical layout pattern** without flagging it in the response.
6. **Never use emojis** in shipping UI strings or in code unless the user explicitly asked.
7. **Never introduce a new dependency** without asking. The existing toolkit usually covers it.

---

<!-- qd:goals -->
## Goals

| Goal | Statement | Verification | Status |
|---|---|---|---|
| G1 | AI chat interface exists and is accessible to all roles on mobile and desktop | `exists: app/(chat)` | proposed |
| G2 | Role-based access gates all content — guards, admins, managers see only their scope | `agent: verify no cross-role content is accessible by switching role in test` | proposed |
| G3 | Training interface exists with role-specific module paths | `exists: app/(training)` | proposed |
| G4 | Admin dashboard exists for content management and analytics | `exists: app/(admin)` | proposed |
| G5 | Both light and dark mode are fully implemented with no unthemed surfaces | `agent: screenshot all main screens in both modes, verify no hardcoded colors` | proposed |
| G6 | All error states follow the documented phrasing table and feedback helper | `lint: app/` | proposed |

---

<!-- qd:lint-hooks -->
## Lint hooks

| Hook | Value |
|---|---|
| `error-helper` | `showToast` |
| `field-error-class` | `field-error` |
| `app-shell-helper` | [PATH/TO/SHELL HELPER] |
| `icon-package` | lucide-react |
| `tokens-path` | `cortex/src/app/globals.css` |

---

<!-- qd:tokens -->
## Design tokens

The canonical source for every token — light **and** dark — is the file named in `tokens-path` (`cortex/src/app/globals.css`). The `Value` column below is the light-mode literal agents quote most often; the dark-mode override lives in the `.dark` block of that file and is mirrored in the **Dark mode values** companion table below for reference. If a value isn't in the canonical source, it doesn't go in the code.

| Token | Value | Use |
|---|---|---|
| `color.primary` | `#1a4a2e` | Brand color: active nav, primary CTAs, selected ring |
| `color.onPrimary` | `#ffffff` | Text on primary surfaces |
| `color.ink` | `#111827` | Default body text |
| `color.inkMuted` | `#6b7280` | Secondary text, hints, timestamps |
| `color.background` | `#f8faf8` | Page background |
| `color.surface` | `#fdfefe` | Cards, inputs, modals |
| `color.surfaceRaised` | `#f6f8f6` | Elevated cards, raised surfaces |
| `color.navActive` | `#E0EEBA` | Nav pill active background |
| `color.glow` | `#d1f0d1` | Radial glow center — main canvas only |
| `color.border` | `#e5e7eb` | Default borders, dividers |
| `color.cardBorder` | `#DEE7E4` | Outer card inset border |
| `color.ring` | `#1a4a2e` | Focus ring |
| `color.accentSubtle` | `#D4EC93` | Active thumbs, recording button |
| `color.inputBorder` | `#d4f3e8` | Chat input widget border |
| `color.danger` | `#dc2626` | Error states, destructive actions |
| `color.dangerSurface` | `#fef2f2` | Error banner/toast background |
| `radius.input` | `8px` | Form fields |
| `radius.button` | `8px` | Buttons |
| `radius.card` | `12px` | Cards, modals |
| `radius.pill` | `6px` | Nav active pill, tags |
| `size.fieldHeight` | `40px` | Inputs and buttons — DO NOT VARY |
| `size.formWidth` | `400px` | Standard form/input column width |
| `size.sidebarWidth` | `160px` | Left sidebar — desktop |

### Dark mode values

Reference only — the live source is the `.dark` block of `cortex/src/app/globals.css`. This table is human documentation; the lint parser reads only the `Value` table above.

| Token | Dark value |
|---|---|
| `color.primary` | `#71b7a2` |
| `color.onPrimary` | `#0a0a0a` |
| `color.ink` | `#ffffff` |
| `color.inkMuted` | `#c5c5c5` |
| `color.background` | `#1a1a1a` |
| `color.surface` | `#1c1c1c` (swapped with the document-page token; pages read `--doc-page-bg: oklch(0.27 0 0)`) |
| `color.surfaceRaised` | `oklch(0.30 0 0)` |
| `color.navActive` | `oklch(0.32 0.04 160)` |
| `color.glow` | — (light only; `--canvas-glow: none` in dark) |
| `color.border` | `oklch(0.38 0 0)` (neutral gray — matches light-mode contrast) |
| `color.cardBorder` | `#355e50` |
| `color.ring` | `#7fc3af` |
| `color.accentSubtle` | `oklch(0.32 0.04 160)` |
| `color.inputBorder` | `#355e50` |
| `color.danger` | `#dc2626` (same) |
| `color.dangerSurface` | `#fef2f2` (same) |

### Extended token families

Beyond the core map, `cortex/src/app/globals.css` is also canonical for these families (light + dark variants live there; never inline siblings): document-page tokens (`--doc-page-bg`, `--doc-text`, `--doc-text-muted`, `--doc-text-faint`, `--doc-callout-bg`, `--doc-callout-border`), illustration tokens (`--il-*`), shadow tokens (`--shadow-modal-panel`, `--shadow-input-widget`, `--shadow-floating`, `--shadow-thumb*`, `--shadow-ai-send-button`), overlay/surface tokens (`--scrim`, `--surface-glass`, `--control-border`, `--progress-track`, `--background-fileview`), ambient tokens (`--blob-1`, `--blob-2`, `--canvas-glow`, `--card-glow-shadow`), and matching-pair accents (`--match-pair-*`). Cortex tokens are registered in the `@theme inline` block, so components use utility classes (`bg-surface`, `text-doc-text-muted`, `border-input-border`) instead of inline `style` objects.

---

<!-- qd:spacing -->
## Spacing

| Spacing token | Value | Use |
|---|---|---|
| `space.page` | `32px` | Page edge padding (desktop); `16px` mobile |
| `space.block` | `48px` | Between major content blocks |
| `space.section` | `32px` | Between sections inside a content block |
| `space.field` | `16px` | Between stacked form fields |
| `space.inline` | `8px` | Between inline siblings (icon ↔ label, button groups) |

---

<!-- qd:typography -->
## Typography

| Role | Family | Size / leading | Weight |
|---|---|---|---|
| H1 (screen titles) | Plus Jakarta Sans | `28px / 36px` desktop, `22px / 30px` mobile | 700 |
| H2 (section titles) | Plus Jakarta Sans | `20px / 28px` | 600 |
| Body | Plus Jakarta Sans | `15px / 24px` | 400 |
| Label / button | Plus Jakarta Sans | `14px / 20px` | 600 |
| Caption / timestamp | Plus Jakarta Sans | `12px / 16px` | 500 |
| Nav item | Plus Jakarta Sans | `14px / 20px` | 500 |

---

## Signature visual technique

**Corner smoothing — 60% on all non-zero radii.**

Every element with a `border-radius` greater than `0` must use 60% corner smoothing (Figma's squircle setting). In CSS this is `corner-shape: squircle` paired with the element's `border-radius`. Never use standard rounded corners on cards, buttons, inputs, avatars, pills, or modals — squircle only.

```css
/* Canonical corner smoothing recipe */
border-radius: var(--radius); /* or any token radius > 0 */
corner-shape: squircle;
```

**Surfaces it must be applied to:** every element where `border-radius > 0` — buttons, inputs, cards, modals, nav pills, avatars, badges, toasts, dropdowns.

**Never applied to:** elements with `border-radius: 0` (full-bleed sections, table rows, dividers).

---

**Soft green radial glow on the main canvas.**

A subtle luminous bloom — centered on the main content area — using a radial gradient from `color.glow` at low opacity to transparent. It grounds the content area without competing with it. Applied to every main canvas background. Never applied to the sidebar, modals, or secondary panels.

```css
/* Canonical glow recipe — the shared `.canvas-glow` class in globals.css.
   Light mode layers --canvas-glow over --surface; dark mode resolves to none. */
.canvas-glow {
  background: var(--canvas-glow), var(--surface);
}
```

**Surfaces it must be applied to:**
- Main canvas / content area on every screen (chat, training, library, dashboard) — via `.canvas-glow` (with a `bg-transparent` PageHeader so the bloom reads through the top)
- Empty states on the main canvas — the AI chat empty state uses the richer animated variant (`--blob-1`/`--blob-2` drifting radials), which counts as this technique

**Never applied to:**
- Sidebar / left nav
- Modals and overlays
- Admin dashboard data tables
- Right-side panels (chat history, etc.)

---

## Layout pattern

**Desktop — sidebar + canvas:**

```
┌──────────────┬──────────────────────────────────────┐
│              │  Breadcrumb                [Actions] │
│   Sidebar    ├──────────────────────────────────────┤
│              │                                      │
│  Dashboard   │         Main canvas                  │
│  AI Chat ●   │         (with radial glow)           │
│  Library     │                                      │
│  Training    │                                      │
│              │                                      │
├──────────────┤                                      │
│ [Avatar]     │                                      │
└──────────────┴──────────────────────────────────────┘
```

**Mobile — bottom tab bar + full canvas:**

```
┌──────────────────────────┐
│  Breadcrumb              │
├──────────────────────────┤
│                          │
│    Main canvas           │
│    (with radial glow)    │
│                          │
├──────────────────────────┤
│ [Dash] [Chat] [Lib] [Tr] │
└──────────────────────────┘
```

**Canonical reference:** see the **Canonical examples** table.

**Active nav state:** pill-shaped highlight using `color.navActive` background and `color.primary` text + icon. Never underline, never border — pill only.

---

## App-shell protocol

The shell owns: left sidebar (desktop) / bottom tab bar (mobile), breadcrumb, and the role indicator.

**Sidebar background rule:** the sidebar is always `bg-transparent` — it never paints its own background. The page wrapper carries the background color (`--background`). This is what allows the main content card's shadow to bleed over the sidebar without clipping. Never set a background on the sidebar element or `--sidebar` token.

Screens communicate with the shell by:
- **Reporting modal/overlay state** — when a screen opens a modal, it signals open state so the nav and breadcrumb yield
- **Never rendering their own nav, breadcrumb, or role indicator** — these are shell-only elements
- **Role gating at the route level** — the shell enforces role-based route access; screens do not gate themselves independently

The active user's role is always visible in the sidebar near the avatar (desktop) or accessible from the profile tab (mobile). Screens read the role from the auth context — they never receive it as a prop.

---

<!-- qd:errors -->
## Errors & validation

**Validation flow:**

1. User submits.
2. Bad fields get the class named in `field-error-class` for the error ring.
3. A single toast/banner appears via the helper named in `error-helper` (`showToast` from `cortex/src/components/ui/toast.tsx`; `<Toaster />` is mounted by the app shell). Never inline error text under a field unless the layout pattern documents it.
   - **Documented exception:** an AI chat response failure renders inline beneath the message with a "Try again" action (the phrasing-table copy verbatim) — retry belongs next to the failed answer, not in a corner toast.
4. The toast/banner lives at the top-right (desktop) or top (mobile) with a close button — neutral surface, no all-red backgrounds.
5. When the user edits a field, clear that field's error state immediately on `onChange`.

**Phrasing table:**

| Situation | Title | Description |
|---|---|---|
| Login fails (wrong credentials) | "Couldn't sign in" | "Check your credentials and try again." |
| Session expires mid-shift | "Your session has ended" | "Sign in again to continue." |
| AI chat fails to respond | "Something went wrong" | "Cortex couldn't get a response. Try again in a moment." |
| Access outside role | "Access restricted" | "This content isn't available for your role. Contact your manager if you think this is a mistake." |
| Content fails to load | "Content unavailable" | "We couldn't load this. Check your connection and try again." |

**Tone:** plain, neutral, no blame, no exclamation marks. Sentence case. End descriptions with a full stop.

---

## Chat non-answers

The AI chat only shows a citation chip when the answer is grounded in a real Library section. When it can't ground an answer, it deflects with one of the two messages below — **rendered with no citation chip** (a chip implies a backing source; a non-answer has none). These are distinct from the *chat failure* case above ("Cortex couldn't get a response") — a failure offers a retry; a deflection does not.

The resolver (`resolveResponse` in `cortex/src/lib/chat-mock.ts`) is a three-tier gate: a question that matches a topic gets a grounded answer with citations; one that hits the domain vocabulary but no topic is `not-found`; one that hits neither is `out-of-scope`. This mirrors a real retrieval-confidence gate and is the seam a real backend plugs into.

| Situation | Message | Affordance |
|---|---|---|
| `out-of-scope` — question isn't about Avante's security operations at all | "I'm focused on Avante's security operations — protocols, procedures, and site guidelines. I can't help with that one. Try asking about access control, incident reporting, patrols, emergencies, or shift handover." | none |
| `not-found` — within security operations, but no Library content covers it | "That's within security operations, but I don't have anything on it in the Library yet. Try rephrasing, or ask your manager if you think it should be here." | inline "Browse the Library" link (→ `/library`) |

**Tone:** same as the phrasing table — plain, no blame, no exclamation marks, sentence case. First-person ("I…"); never refer to Cortex in the third person mid-conversation. Escalate to "your manager" (universal across shift/certification status), never "shift supervisor".

---

## Modals / overlays

- **Backdrop:** `rgba(17, 24, 39, 0.4)` at `160ms` fade.
- **Card:** scales `0.96` → `1` over `200ms` with `cubic-bezier(0.32, 0.72, 0, 1)`.
- **Outside click closes the modal.** The card itself uses `onClick={(e) => e.stopPropagation()}`.
- **Body scroll is locked** while open.
- **Headers:** modal title uses H2 role. Close affordance is top-right inside a ≥44×44 hit target.
- **Footer:** secondary left, primary right. If primary is disabled, 50% opacity — never hidden.

---

## CTAs & wording

- **Primary CTA**: `color.primary` background, `color.onPrimary` text, `radius.button`, `size.fieldHeight`, signature glow never applied to buttons.
- **Secondary CTA**: outlined variant — `color.primary` border and text, transparent background.
- **Tertiary / "Skip" / "Not now"**: muted text link, not a button.
- **Button text is sentence case**: "Start training", "Ask Cortex", "Save changes", "Back to dashboard".
- **Disabled state**: 50% opacity + `cursor: not-allowed`. Never hidden.

---

## Selection patterns

- **Pick-one groups**: clicking a different item switches selection; clicking the already-selected item does nothing.
- **Required steps default to a sensible value** where possible.
- **Selected state**: `color.navActive` background + `color.primary` text/icon + left border accent `2px solid color.primary` for list items; pill highlight for nav.

---

<!-- qd:empty-states -->
## Empty states

| Surface | Empty copy | Non-empty copy |
|---|---|---|
| AI chat (new session) | "How can I help you [Name]? Ask anything about protocols, procedures, or guidelines." | — |
| Chat history | "No previous conversations." | "Today / Yesterday / Last 7 days" grouped list |
| Training modules | "No modules assigned to your role yet." | Module list |
| Knowledge library | "No documents available." | Document grid |
| Admin content list | "No content added yet. Start by uploading a document." | Content table |
| Analytics | "No activity data yet." | Charts and metrics |

---

## Iconography

- **One icon library** — `lucide-react`. Stroke width `1.5`, sizes `16px` (inline) / `20px` (nav) / `24px` (feature icons).
- **Color icons via `currentColor`** — never hex an icon directly.
- **Brand SVGs** live in `public/brand/` — wrap them, never edit exports directly.

---

<!-- qd:animation -->
## Animation timing

| Where | Duration | Easing |
|---|---|---|
| Screen content fade in | `200ms` | `ease-out` |
| Modal backdrop fade | `160ms` | `ease-out` |
| Modal card scale + opacity | `200ms` | `cubic-bezier(0.32, 0.72, 0, 1)` |
| Nav active state transition | `150ms` | `ease-out` |
| Chat history panel slide | `220ms` | `cubic-bezier(0.32, 0.72, 0, 1)` |
| Hover / focus color shifts | `100ms` | `ease-out` |
| AI response streaming cursor | `600ms` | `ease-in-out` (pulse) |
| AI thinking indicator (orb breathe) | `1200ms` | `ease-in-out` (infinite) |
| AI thinking status shimmer sweep | `2200ms` | `linear` (infinite) |
| Message / chapter content entrance | `200ms` | `ease-out` (translateY 8px + fade) |
| Citation chip pop-in | `150ms` | `ease-out`, staggered `60ms` per chip |
| Card hover lift | `150ms` | `ease-out` (translateY −2px + shadow; dark mode uses background step) |
| Quiz wrong-answer shake | `250ms` | `ease-out` (±2px) |
| Check pop-in (quiz / results) | `250–300ms` | `cubic-bezier(0.32, 0.72, 0, 1)` |
| Results glow bloom | `400ms` | `ease-out` |
| Progress fill sweep | `300ms` | `ease-out` (bars animate from 0 on mount; segments stagger `80ms`) |

All animation collapses to imperceptible durations under `prefers-reduced-motion: reduce` (global guard in `globals.css`).

---

## File hygiene

- **One screen = one file.** Pickers, cards, and small reusable bits get their own file.
- **Primitive UI** lives under `components/ui/`.
- **Brand assets** live under `public/brand/` — do not edit; wrap them.
- **No new dependencies** without asking.
- **Naming:** PascalCase for components, camelCase for hooks/utils, kebab-case for routes and files.

---

## Pre-edit checklist (run this mentally before every change)

1. **Tokens?** Am I introducing any value not already in the token map?
2. **Glow?** Is the radial glow applied to the main canvas?
3. **Layout?** Does the screen follow the canonical layout pattern?
4. **Role boundary?** Is this content/route gated correctly for the user's role?
5. **App-shell protocol?** Am I reporting nav/overlay state correctly?
6. **Errors?** Are validation messages routed through the helper, with wording matching the phrasing table?
7. **Animation?** Am I reusing documented timings?
8. **Wording?** Sentence case, no emojis, no exclamation marks?
9. **Accessibility?** Keyboard-reachable, labeled, hit-target ≥44×44, contrast AA?
10. **Both modes?** Does this work in light and dark mode?

---

<!-- qd:canonical -->
## Canonical examples (look here when in doubt)

| Pattern | Look at |
|---|---|
| Canonical layout (sidebar + canvas) | `cortex/src/app/(app)/layout.tsx` |
| App-shell protocol | `cortex/src/components/cortex-sidebar.tsx` |
| Modal open/close + body lock | `cortex/src/components/knowledge-check/KCDetailModal.tsx` |
| Radial glow on canvas | `cortex/src/app/(app)/chat/page.tsx` |
| Nav active pill state | `cortex/src/components/cortex-sidebar.tsx` |
| Empty-state copy | `cortex/src/app/(app)/chat/page.tsx` |
| Chat non-answers + citation gating | `cortex/src/lib/chat-mock.ts` (`resolveResponse`, `ChatResponse`) |
| Error wiring (toast + field-error) | `cortex/src/components/ui/toast.tsx` (helper + `<Toaster />`); `.field-error` in `globals.css` |
| Mobile bottom tab bar | `cortex/src/components/mobile-tab-bar.tsx` |
| AI thinking indicator + streaming caret | `cortex/src/components/chat/ThinkingIndicator.tsx` |
| Primary CTA (Button `size="cta"`) | `cortex/src/components/ui/button.tsx` |
| Role indicator (sidebar footer) | `cortex/src/components/cortex-sidebar.tsx` + `cortex/src/lib/user-mock.ts` |
| Role-gated route | [PATH — not built yet: no auth middleware exists] |

---

## What "done" looks like for any change

- [ ] Pre-edit checklist passes (or deviations are explicitly called out).
- [ ] No regressions: surfaces I didn't touch still look and behave identically.
- [ ] Verified in the live preview / running app, not just type-checked.
- [ ] Tokens-only — no inline magic numbers or colors.
- [ ] Both light and dark mode verified.
- [ ] Role boundary respected — no cross-role content visible.
- [ ] Accessibility floor met (focus, labels, hit targets, contrast).

---

## When this doc and reality disagree

If you find code that breaks a rule above, it's either (a) a bug to flag, or (b) this doc is stale and needs updating. **Don't silently follow the broken pattern** — flag it. To run a full staleness pass, ask for `design-vision` reconcile mode.

---

## Wiring

Run `npx design-fleet vision --wire` to wire this file into Claude Code automatically. It writes a marker-delimited block in `CLAUDE.md` that imports `@VISION.md` so this contract loads before every non-trivial change.
