# VISION — [PROJECT NAME]

<!-- qd-vision-template: v1 -->

> **Read this before every non-trivial change.** This is the hard-rails contract for working in this project. If a rule below conflicts with something you're about to do, **stop and ask** — don't quietly improvise. The goal is one coherent product, not seven flavors of "almost right."

Unfilled values look [LIKE-THIS]. A section full of placeholders is declared not-applicable-yet, not missing — `design-review` reports it as such instead of failing. Never delete a section to hide an unfilled value.

---

## North Star

> One short paragraph. State the *problem the product solves*, the *user it serves*, the *one outcome the product is optimized for*, and any *standing constraint that overrides convenience* (demo vs. production, desktop-first vs. mobile-first, performance budget, accessibility floor).
>
> Example shape: "A short, friendly, desktop-first [PRODUCT] that gets [USER] from [START STATE] to [END STATE] in [N] steps. Every screen must feel like the same hand drew it. [STANDING CONSTRAINT]."

[NORTH STAR PARAGRAPH]

---

## The unbreakable rules (read these first)

**ALWAYS**

1. **Use design tokens.** Every color, font, radius, spacing, and size MUST come from the canonical token map (see **Design tokens**). Inlining a one-off "close enough" value is the single most common drift vector.
2. **Apply the project's signature visual technique** to every applicable surface (see **Signature visual technique**). If this project has none, that section says so explicitly — but recognize what it costs in consistency.
3. **Follow the canonical layout pattern** for the surface you're on (see **Layout pattern**). Don't invent new compositions for one screen.
4. **Route errors through the feedback helper** — never inline error text under a field unless the layout pattern explicitly mandates it. See **Errors & validation**.
5. **Communicate with the app shell through the documented protocol** (footer config, modal state, breadcrumb state). Never render your own copy of a global UI element.
6. **Report modal/overlay state** so global UI (sticky footers, breadcrumbs, page chrome) can yield while the overlay owns the viewport.
7. **Use only the typefaces in the type ramp** (see **Typography**). No system fonts. No new families.
8. **Match the existing wording style.** Sentence case for buttons and labels. Plain language. No emojis in shipping copy. No "Oops/Whoops/Yay/Awesome." See **CTAs & wording**.
9. **Treat the latest client/PM feedback as source of truth** for copy and behavior. When in doubt, search the feedback folder before improvising. (delete if not applicable)

**NEVER**

1. **Never introduce a new color, font, radius, or spacing value** without adding it to the token map first. No siblings of existing tokens.
2. **Never let a required selection end up empty** through a re-click or background click. The only no-selection path is an explicit "skip" affordance.
3. **Never use inline error text** under a field unless that's the documented pattern. Default is toast/banner + a field-error state class.
4. **Never break the canonical layout pattern** without flagging it in the response.
5. **Never amend a previous commit** to "fix" something; create a new one. Hooks should block this anyway. (delete if not applicable)
6. **Never use emojis** in shipping UI strings or in code unless the user explicitly asked.
7. **Never expand a client-approved fixed set** (countries, currencies, plan tiers) without a feedback doc backing it. (delete if not applicable)
8. **Never introduce a new dependency** without asking. The existing toolkit usually covers it.
9. **Never write real network calls / payments / auth** if the project is a demo. (Delete this rule only if the project is production.)

---

<!-- qd:goals -->
## Goals

What the product must achieve, as **verifiable statements** — not vibes. One
row per goal. `npx design-fleet verify` machine-checks what it can and treats
a failing `met` goal as a regression.

- **Statement** — one checkable sentence ("A pricing page exists at /pricing
  and passes lint", not "make pricing feel premium").
- **Verification** — how to check it. Three forms: `exists: <repo-relative
  path>` (file/dir must exist), `lint: <path>` (design lint passes on that
  path; bare `lint` means the whole project), or `agent: <how to judge>`
  (needs a design-review session — `verify` reports it, never auto-passes it).
- **Status** — the lifecycle: `proposed` (suggested by the design-contract
  skill's plan mode or a human, not yet adopted) → `active` (being built) →
  `met` (verified done). **Promotion is a human edit** — no agent or loop may
  change `proposed` to `active`, or `active` to `met`, on its own.

| Goal | Statement | Verification | Status |
|---|---|---|---|
| G1 | [ONE CHECKABLE STATEMENT OF WHAT MUST BE TRUE] | [VERIFICATION FORM] | proposed |

---

<!-- qd:lint-hooks -->
## Lint hooks

Machine-readable wiring for `design-review`'s project checks. Exactly these five rows — fill the Value column, never rename a Hook cell. Two kinds of value, and mixing them up breaks the wiring:

- **Identifiers** — `error-helper`, `field-error-class`, `icon-package`. The name exactly as it appears in code: a function name (`showError`), a CSS class name (`field-error`), an npm package name (`lucide-react`).
- **Paths** — `app-shell-helper`, `tokens-path`. Repo-relative file paths, e.g. `components/AppShell.tsx`, `src/tokens.css`.

Two hooks feed the deterministic lint engine today: `error-helper` is the function name P5 builds its call-site regex from (a file path here silently deactivates the check), and `tokens-path` names the canonical tokens file P1 exempts from the token-literal check. The other three — `field-error-class`, `app-shell-helper`, `icon-package` — inform the LLM-judged half of `design-review` (Category 8's judgment checks).

| Hook | Value |
|---|---|
| `error-helper` | [ERROR HELPER FUNCTION NAME] |
| `field-error-class` | [FIELD ERROR CLASS NAME] |
| `app-shell-helper` | [PATH/TO/SHELL HELPER] |
| `icon-package` | [ICON PACKAGE NAME] |
| `tokens-path` | [PATH/TO/TOKENS] |

---

<!-- qd:tokens -->
## Design tokens

The canonical source is the file named in `tokens-path` above (Tailwind config, CSS variables file, theme JSON, or token export). Screen-local token aliases must mirror it exactly. The table below is the small subset agents quote most often — if a value isn't in the canonical source, it doesn't go in the code.

Filled cells are backticked literals in canonical form: tinted near-black/near-white like `#0f172a` / `#fcfcfd` — never pure black or pure white — pixel sizes like `40px`, radii like `8px`.

| Token | Value | Use |
|---|---|---|
| `color.primary` | [HEX] | Brand color: primary CTAs, selected ring, active links |
| `color.onPrimary` | [HEX] | Text on primary surfaces |
| `color.ink` | [HEX] | Default body text |
| `color.background` | [HEX] | Page background |
| `color.surface` | [HEX] | Cards, inputs, modals |
| `color.muted` | [HEX] | Secondary text, hints |
| `color.border` | [HEX] | Default borders |
| `color.danger` | [HEX] | Error states, destructive actions |
| `radius.input` | [PX] | Form fields (e.g. `8px`) |
| `radius.button` | [PX] | Buttons (e.g. `8px`) |
| `radius.card` | [PX] | Cards, modals (e.g. `12px`) |
| `size.fieldHeight` | [PX] | Inputs and buttons — DO NOT VARY (e.g. `40px`) |
| `size.formWidth` | [PX] | Standard form width (e.g. `400px`) |

If you need a value that isn't there, add it to the canonical source first, then use it.

---

<!-- qd:spacing -->
## Spacing

Inter-block gaps are named here so they're not reinvented per file. Values sit on the 4/8/12/16/24/32/48/64 scale — e.g. `48px` between content and footer blocks, `32px` between sections inside the content block, `16px` between fields. Anything off-scale is a lint violation.

| Spacing token | Value | Use |
|---|---|---|
| `space.page` | [PX] | Page edge padding |
| `space.block` | [PX] | Between major content blocks (content ↔ footer) |
| `space.section` | [PX] | Between sections inside a content block |
| `space.field` | [PX] | Between stacked form fields |
| `space.inline` | [PX] | Between inline siblings (icon ↔ label, button groups) |

---

<!-- qd:typography -->
## Typography

Three families is plenty. Two is better. Each role has exactly one (family, size, leading, weight). Never mix families in the same word, button, or sentence. Display families are for display moments only — **never** for body copy or inputs.

| Role | Family | Size / leading | Weight |
|---|---|---|---|
| H1 (screen titles) | [DISPLAY FAMILY] | [SIZE/LEADING] desktop, [SIZE/LEADING] mobile | [400-700] |
| H2 (section titles) | [DISPLAY FAMILY] | [SIZE/LEADING] | [400-700] |
| Body | [BODY FAMILY] | [SIZE/LEADING] | [400] |
| Label / button | [BODY FAMILY] | [SIZE/LEADING] | [600] |
| Link (small) | [BODY FAMILY] | [SIZE/LEADING] | [600] |
| Reassurance / footnote | [BODY FAMILY] | [SIZE/LEADING] | [500] |

---

## Signature visual technique

> Most polished design systems have one or two "signature techniques" — the thing that makes screens recognizably yours. Examples: continuous corners (`corner-shape: squircle`), a glass-blur recipe, a specific layered drop-shadow, a hand-drawn underline on links, a precise grid, a motion-easing fingerprint.
>
> Define yours here. Show the **canonical code pattern**. List **every surface it must be applied to**. If a new surface goes in without it, the inconsistency reads instantly.
>
> If this project deliberately has no signature technique, write "None — by decision" so the absence reads as a choice, not a gap.

[SIGNATURE TECHNIQUE DESCRIPTION CODE PATTERN AND SURFACE LIST]

---

## Layout pattern

> Pick the *one* layout pattern most screens follow. ASCII-sketch it. Name the canonical reference file. New screens diff against that reference.

```
┌─────────────────────────────────┐
│        [ chrome / nav ]         │
│                                 │
│            <Title>              │
│         <Subtitle>              │
│                                 │
│      <Form / content body>      │
│                                 │
│   [Secondary]      [Primary]    │
└─────────────────────────────────┘
```

**Canonical reference:** see the **Canonical examples** table. When a screen feels visually off, diff it against that file.

**Spacing:** all inter-block gaps come from the **Spacing** table above. Don't reinvent them per file.

---

## App-shell protocol

> Every screen needs a documented contract for talking to the app shell. The minimum: how the screen reports its action buttons (footer/header CTAs), how it reports overlay state (modals/sheets/popovers), how it requests navigation.

[APP SHELL CONTRACT]

Example contract shape:

```ts
onFooterChange({
  primaryLabel: '...',
  primaryHandler: handleStable,
  primaryDisabled: false,
  secondaryLabel: '...',
  secondaryHandler: handleBackStable,
  showSkip: true,
  skipHandler: handleSkipStable,
});
return () => onFooterChange(null);
```

**Use stable refs** for any handlers passed to global UI so the global UI doesn't tear down + remount on every state change in the screen.

If a screen opens a modal/sheet/overlay, it MUST also report that state so global UI (sticky footers, breadcrumbs) can yield. Failure to do this is the most common cause of "double-button" bugs.

---

<!-- qd:errors -->
## Errors & validation

**Validation flow:**

1. User submits.
2. Bad fields get the class named in `field-error-class` (see **Lint hooks**) for the error ring.
3. A single toast/banner appears via the helper named in `error-helper`. Never inline error text under a field unless the layout pattern documents it.
4. The toast/banner lives at a single documented position with a close button — neutral surface, no all-red.
5. When the user edits a field, clear that field's error state immediately on `onChange`.

**Phrasing rules** — fill the table with every error string in the product. Once it's in the table, it can't drift:

| Situation | Title | Description |
|---|---|---|
| [ERROR SCENARIO] | [EXACT WORDING] | [EXACT WORDING OR NONE] |

**Tone:** plain, neutral, no blame, no exclamation marks. Sentence case for both title and description. End descriptions with a full stop. Don't enumerate accounts ("we couldn't find that account") — say only what's actionable to the user.

---

## Modals / overlays

- **Backdrop:** [BACKDROP COLOR] at [OPACITY], [DURATION] fade (e.g. `rgba(15, 23, 42, 0.4)`, `160ms`).
- **Card:** scales [START SCALE] → [END SCALE] over [DURATION] with [EASING] (e.g. `0.96` → `1` over `200ms` with `cubic-bezier(0.32, 0.72, 0, 1)`).
- **Outside click closes the modal.** The card itself uses `onClick={(e) => e.stopPropagation()}`.
- **Body scroll is locked** while open: `document.body.style.overflow = 'hidden'` on enter, `''` on cleanup (and on unmount).
- **Headers:** modal title uses the same display role as screen H1, at the documented mobile/desktop sizes. Close affordance is top-right inside its own ≥44×44 hit target.
- **Footer:** secondary left, primary right. If the primary is disabled, it shows at 50% opacity, never hidden.
- **Edit-flow overlays reuse the same component** as the create flow (one component, mode prop), not a parallel implementation.

**Popovers / small popups** close on **outside click**. To prevent the trigger from immediately reopening the popover, forward the trigger ref into the popover so the click-outside handler can exclude it.

---

## CTAs & wording

- **Primary CTA**: brand color, label/button type role, `radius.button`, `size.fieldHeight`, signature technique applied, `color.onPrimary` text.
- **Secondary CTA**: documented variant (e.g., outlined, ghost) — never reinvented per screen.
- **Tertiary / "Skip" / "Not now"** is a muted text link, **not a button**. Used only for genuinely optional steps.
- **State-aware labels** are encouraged: reflect state in the label rather than disabling silently. (E.g., "Continue to checkout" when a selection exists, "Continue" when it doesn't.)
- **Disabled state** uses a documented opacity + `cursor: not-allowed`. Always present, never just hidden.
- **Button text is sentence case**: "Save learner", "Add learners", "Create account", "Back to sign in". Not Title Case.

---

## Selection patterns

- **Pick-one groups**: clicking a different item switches the selection; clicking the already-selected item **does nothing**. Background clicks **do not** clear. The only no-selection path is an explicit skip affordance.
- **Pick-many groups**: clicking toggles. Background clicks do not clear.
- **Required steps default to a sensible value** when possible.
- **Selected state** uses the documented combination of (border, fill, indicator): [SELECTED STATE RECIPE]. Don't invent new selected-state visuals per screen.

---

<!-- qd:empty-states -->
## Empty states

Every collection-based screen needs an empty-state copy line directly under the title. It MUST tell the user what's missing and what to do next. Empty states are never silent. Example pair: "Add your first item to get started." / "3 items added."

| Surface | Empty copy | Non-empty copy |
|---|---|---|
| [SURFACE] | [EXACT WORDING] | [EXACT WORDING] |

---

## Iconography

- **One icon library** — the package named in `icon-package` (see **Lint hooks**). Stroke width and sizes are part of the design system, not the icon's choice.
- **Exported / brand SVGs** belong in a documented folder. Wrap them; don't trust deeply-nested percentage transforms inside Figma/SVG exports — they render brittle. If a sub-icon looks off, rewrite it as a single clean centered SVG before debugging the export.
- **Color icons via `currentColor` or stroke = brand token.** Never hex an icon in place.

---

<!-- qd:animation -->
## Animation timing

If you need an animation that isn't here, **ask first**. New durations and easings should be rare. Filled cells are backticked literals — durations in ms or s (`200ms`), easings as keywords or `cubic-bezier(0.32, 0.72, 0, 1)`.

| Where | Duration | Easing |
|---|---|---|
| Screen content fade in/out | [DURATION] | [EASING] |
| Modal backdrop fade | [DURATION] | [EASING] |
| Modal card scale + opacity | [DURATION] | [EASING] |
| Progress / state transitions | [DURATION] | [EASING] |
| Hover / focus colors | [DURATION] | [EASING] |

---

## File hygiene

- **One screen = one file.** Pickers, cards, and small reusable bits get their own file.
- **Primitive UI** (buttons, toggles, feedback helpers) lives under [PRIMITIVES PATH].
- **Exported / brand assets** live under [ASSETS PATH] — do not edit; wrap them.
- **No new dependencies** without asking.
- **Comment the "why," not the "what."** Especially around any non-obvious framework pattern (stable refs, effect dependency choices, ref forwarding for outside-click exclusion).
- **Naming:** PascalCase for components, camelCase for hooks/utils, kebab-case for routes/files in route-based frameworks. Pick one convention per category and stick to it.

---

## Pre-edit checklist (run this mentally before every change)

1. **Tokens?** Am I introducing any value not already in the token map?
2. **Signature visual technique?** Is it applied to every applicable surface?
3. **Layout?** Does the screen follow the canonical layout pattern?
4. **App-shell protocol?** Am I reporting footer config / overlay state correctly?
5. **Errors?** Are validation messages routed through the helper, with wording matching the phrasing table?
6. **Selection?** If this is a required-selection step, can the user end up in a no-selection state by mistake?
7. **Animation?** Am I reusing documented timings, not inventing new ones?
8. **Wording?** Sentence case, no emojis, no exclamation marks, matches feedback docs?
9. **Accessibility?** Are interactive elements keyboard-reachable, labeled, hit-target ≥44×44?
10. **State leaks?** Does this change introduce any global state, side effect, or imperative DOM mutation that the rest of the app doesn't know about?

If you can't answer "yes" to all relevant items, **explain the deviation in the response to the user before shipping the change.**

---

<!-- qd:canonical -->
## Canonical examples (look here when in doubt)

For each repeated pattern, name *the one file that does it right*. Future changes diff against the canonical example, not against each other.

| Pattern | Look at |
|---|---|
| Canonical layout | [PATH] |
| App-shell protocol + stable refs | [PATH] |
| Modal open/close + body lock | [PATH] |
| State-aware primary CTA | [PATH] |
| Outside-click popover | [PATH] |
| Signature visual technique applied | [PATH] |
| Empty-state copy + count | [PATH] |
| Error wiring (toast + field-error) | [PATH] |
| Form validation pattern | [PATH] |

---

<!-- qd:lint-ignore -->
## Lint exclusions (optional)

Paths where raw literals are *content, not styling* — component-gallery/docs
pages that render token values as examples, hand-painted preview art, demo
fixtures whose numbers aren't design values. The deterministic lint skips
matching files and **reports the count** (never silent). Gitignore-style
globs, repo-relative: `*` within a segment, `**` any depth, `dir/**` a
subtree. Leave the placeholder row if you have no exclusions — this section
is config, not part of the contract, so it never counts as "unfilled".

| Path |
|---|
| [PATH GLOB] |

---

## What "done" looks like for any change

A change is shippable when **all** of the following are true:

- [ ] Pre-edit checklist passes (or deviations are explicitly called out).
- [ ] No regressions: surfaces I didn't touch still look and behave identically.
- [ ] Verified in the live preview / running app, not just type-checked.
- [ ] Tokens-only — no inline magic numbers or colors.
- [ ] Wording matches the feedback docs.
- [ ] Global UI (sticky footers, breadcrumbs, headers) still behaves correctly across breakpoints.
- [ ] Accessibility floor met (focus, labels, hit targets, contrast).
- [ ] The summary I send the user describes the **actual change** (file + line), not the intent.

---

## When this doc and reality disagree

If you find code that breaks a rule above, it's either (a) a bug to flag, or (b) this doc is stale and needs updating. **Don't silently follow the broken pattern** — flag it. If you update this doc, also call that out in your response so the user can review. To run a full staleness pass, ask for `design-contract` reconcile mode.

---

## Wiring

This file is wired into agents automatically — do not hand-write imports. Run `npx design-fleet vision --wire` (or let the `design-contract` skill do it): it writes a thin `alwaysApply` pointer rule at `.cursor/rules/vision.mdc` and a marker-delimited block in `CLAUDE.md` that imports `@VISION.md`. Re-running is idempotent; the markers keep hand-edits outside the block safe.

---

## Appendix: Migration targets (optional)

> **Optional and size-capped.** Only present on projects migrating toward this contract. Keep it to at most 10 bullets of the form "from [CURRENT STATE] to [TARGET]" — anything bigger belongs in an issue tracker, not the contract. Delete the appendix when migration is done; an empty appendix is staleness.

[MIGRATION TARGETS OR DELETE THIS APPENDIX]
