---
name: design-review
description: Two-altitude design evaluator for a UI artifact or just-written UI code. DETERMINISTIC FLOOR (lint/validate/check/verify): validates recently written or modified UI code against active Design Fleet rules — and, when the project has a VISION.md contract, against its exact tokens, phrasing, and timing tables — producing a concrete pass/fail report with violation IDs, locations, and suggested fixes. SUBJECTIVE CEILING (critique/review/feedback/improve/polish/refine): delivers a systematic visual and behavioral critique of a screenshot, component file, or live URL, organized into visual design, interface design, interaction consistency, and user context. Includes a Nielsen Heuristic Mode for explicit requests like "heuristic review", "usability audit", or "the 10 heuristics", rating violations cosmetic/minor/major/catastrophic. For codebase-wide scans, vision drafts, or vision reconciliation use design-contract; for building UI use frontend-design.
argument-hint: "[description, file path, glob, screenshot, or 'last changed files']"
---

# Design Review

A two-altitude design evaluator. It answers two different questions about the same work:

- **The deterministic floor (blocking)** — *Does this code follow the rules?* Validate UI code against active Design Fleet rules and, when present, the project's VISION.md contract. Produces a concrete pass/fail report with file locations, violation descriptions, and inline fixes. This is the verification layer — rules tell the agent what to do, this confirms it did. Checks are objective and grep-able; two agents running the same check on the same code get the same result.
- **The subjective ceiling (advisory)** — *Is this good?* Analyze a UI screenshot or component code and deliver specific, actionable feedback organized by visual design, interface design, interaction consistency, and user context. Has two output modes: the default 7-section critique, and a formal [Nielsen Heuristic Mode](#nielsen-heuristic-mode) for explicit usability-audit requests.

The floor catches what regex can catch. The ceiling catches what only judgment can. They never blend into one verdict — a lint report is factual pass/fail; a critique is opinionated craft feedback. Run the floor first when verifying generated code, then raise to the ceiling when the user wants polish or a usability evaluation.

---

## When to Use

**Floor (lint / validate):**

- After generating UI with `frontend-design`
- After modifying existing UI code
- User says "lint", "check", "validate", "verify", "does this follow the rules"
- As the final step in a build → lint chain
- When reviewing a PR or diff for design quality

**Ceiling (critique / heuristic):**

Trigger on: "critique", "review", "feedback", "what's wrong", "improve", "polish", "refine", "redesign", "analyze this UI", "look at this", or when the user pastes a screenshot/image for evaluation. Also trigger on: "Nielsen review", "heuristic review", "heuristic evaluation", "usability audit", "check against the 10 heuristics" — these activate Nielsen Heuristic Mode (see below).

**Not this skill:** If the user wants to scan an entire codebase for design consistency, generate a VISION.md, or reconcile a vision against reality, use `design-contract`. If they want UI built rather than reviewed or verified, use `frontend-design`. If they ask how to implement or fix an animation, easing, or CSS/SVG pattern, use `frontend-design`. The floor of this skill checks code against specific, grep-able rules; the ceiling evaluates what a specific UI artifact looks and feels like — neither is a codebase-wide audit.

---

# PART 1 — DETERMINISTIC FLOOR (Lint)

Validate UI code against active Design Fleet rules. Produces a concrete pass/fail report with file locations, violation descriptions, and inline fixes.

## What to Lint

Determine the scope. In priority order:

1. **Explicit path** — User provides a file or glob (`components/Button.tsx`, `app/**/*.tsx`)
2. **Recent changes** — Check git diff or recently modified files in the session
3. **Component directory** — User says "check my components" → scan `components/` or `components/ui/`
4. **Full project** — User says "lint everything" → scan all UI files (`**/*.{tsx,jsx,vue,svelte,css,scss}`)

Read every file in scope before running checks. Do not lint files you haven't read.

## Lint Process

Run every applicable check against every file in scope. Each check is a concrete, searchable pattern — not a subjective judgment.

### Category 1: Visual Design (from `visual-design.mdc`)

| ID | Check | What to Search For | Violation |
|----|-------|-------------------|-----------|
| V1 | **No pure black shadows** | `box-shadow` values containing `#000`, `rgb(0,0,0)`, `rgba(0,0,0` without tint | Shadows should use tinted dark colors, not pure black |
| V2 | **Layered shadows** | `box-shadow` with only a single layer on elevated elements (cards, modals, dropdowns) | Elevated elements should use multi-layer shadows for depth |
| V3 | **Consistent shadow direction** | Compare `offset-x`/`offset-y` across all `box-shadow` declarations | All shadows should cast in the same direction |
| V4 | **Concentric radius** | Nested elements with `border-radius` — inner radius should equal outer minus gap | Inner radius = outer radius - padding between them |
| V5 | **No pure black/white defaults** | `#000`, `#fff`, `rgb(0,0,0)`, `rgb(255,255,255)` as background or text colors | Use tinted near-black/near-white unless forced-colors mode |
| V6 | **Alpha borders** | `border-color` with hardcoded opaque colors between surfaces | Borders between surfaces should use alpha transparency |
| V7 | **Spacing scale** | Arbitrary spacing values like `13px`, `37px`, `p-[23px]` | Spacing should follow the 4/8/12/16/24/32/48/64 scale |

### Category 2: Typography (from `typography.mdc`)

| ID | Check | What to Search For | Violation |
|----|-------|-------------------|-----------|
| T1 | **No banned fonts** | `font-family` declarations containing `Inter`, `Roboto`, `Arial`, `Open Sans`, `Lato`, `system-ui` as the primary choice (deterministic — runs in `npx design-fleet lint`; a VISION.md-declared family suppresses it) | Use distinctive, project-specific fonts |
| T2 | **Font display swap** | `@font-face` declarations missing `font-display: swap` | Add `font-display: swap` to prevent invisible text |
| T3 | **Antialiasing** | Missing `-webkit-font-smoothing: antialiased` in global styles | Set antialiasing globally |
| T4 | **Type scale** | More than 6 distinct `font-size` values that don't follow a ratio | Consolidate to a type scale |

### Category 3: Color (from `color-contrast.mdc`)

| ID | Check | What to Search For | Violation |
|----|-------|-------------------|-----------|
| C1 | **Hardcoded colors** | Hex codes (`#[0-9a-fA-F]{3,8}`), `rgb()`, `hsl()` in component files (not in globals/config) | Colors should be CSS variables or design tokens |
| C2 | **Gray on color** | `text-gray-*` or gray hex on a colored background | Use a shade of the background color for text |
| C3 | **Dark mode coverage** | Components with explicit light colors but no `dark:` variant or `prefers-color-scheme` | Dark mode should be handled if the project supports it |

### Category 4: Animation (from the `frontend-design` skill's motion layer)

| ID | Check | What to Search For | Violation |
|----|-------|-------------------|-----------|
| A1 | **No bounce/elastic default** | `bounce`, `elastic` easing keywords in non-gesture contexts | Bounce/elastic should be reserved for gesture-driven interactions |
| A2 | **Duration ceiling** | Animation durations exceeding `300ms` for UI elements (not marketing) | UI animations should stay under 300ms — superseded by P7 when VISION.md declares an animation-timing table |
| A3 | **Reduced motion** | `motion.div`, `animate`, `transition`, `@keyframes` without corresponding `prefers-reduced-motion` | Every animation needs a reduced-motion fallback |
| A4 | **Animating layout properties** | Animating `width`, `height`, `padding`, `margin`, `top`, `left` | Animate `transform` and `opacity` only for GPU performance |
| A5 | **Exit animations** | `AnimatePresence` without `exit` prop on children | Elements inside AnimatePresence need `exit` props |

### Category 5: Interaction (from `keyboard-shortcuts.mdc`, `frontend-design`)

| ID | Check | What to Search For | Violation |
|----|-------|-------------------|-----------|
| I1 | **Cursor pointer** | `<button>`, `<a>`, `[role="button"]`, `<select>`, `<summary>` without `cursor: pointer` (check if global rule exists) | Interactive elements must show pointer cursor |
| I2 | **Focus visible** | `outline: none` or `outline: 0` without a `:focus-visible` replacement | Never remove focus outline without replacing it |
| I3 | **Escape closes overlays** | Modal/dialog/drawer components without Escape key handling | Overlays must close on Escape |
| I4 | **Touch-safe hover** | `:hover` styles without `@media (hover: hover)` guard | Hover effects need touch-safe media query |

### Category 6: UX Writing (from `ux-writing.mdc`)

| ID | Check | What to Search For | Violation |
|----|-------|-------------------|-----------|
| W1 | **Placeholder-only labels** | `<input>` with `placeholder` but no associated `<label>` or `aria-label` | Placeholders are not labels — add a real label |
| W2 | **Generic button text** | Buttons with text like "Submit", "Click here", "OK" | Button text should describe the action ("Save changes", "Send message") |
| W3 | **Dev jargon in UI** | `userId`, `null`, `undefined`, `Error 500`, `NaN`, raw field names in visible text | User-facing text should use human language |
| W4 | **Empty states** | Conditional renders that show nothing (empty `<div>`, no fallback) when data is absent | Empty states should guide the user toward action |

### Category 7: Responsive (from `responsive-design.mdc`)

| ID | Check | What to Search For | Violation |
|----|-------|-------------------|-----------|
| R1 | **Viewport zoom** | `<meta name="viewport"` with `maximum-scale=1` or `user-scalable=no` | Never disable pinch-to-zoom |
| R2 | **Fixed widths** | Hardcoded `width: Npx` on containers or layout elements (not icons/avatars) | Layout widths should be responsive |
| R3 | **Absolute dropdowns in overflow** | `position: absolute` inside `overflow: hidden` parent | Use `position: fixed`, portals, or `popover` attribute |

### Category 8: Project Contract (from `VISION.md`)

Only when `VISION.md` exists at the project root. The deterministic half runs as real code — execute it first:

```bash
npx design-fleet lint [path]
```

That command parses the contract's tables and runs the P-checks (exact project values, not heuristics): **P1** token literals match the token map, **P5** error copy matches the phrasing table verbatim, **P7** animation durations on the project allowlist, **P12** canonical example paths exist. It also tightens the generic checks: declared token values suppress V1/V5 false positives, and a declared spacing table replaces V7's generic scale — suppressions are reported, never silent. Include its output in the report under this category, keeping the P-check IDs.

**Lint exclusions.** A `<!-- qd:lint-ignore -->` section in `VISION.md` lists gitignore-style globs for files where raw literals are *content, not styling* — component-gallery/docs pages, hand-painted preview art, demo fixtures. The walker skips matching files and reports the excluded count (`Linting N files (M excluded by lint-ignore)…`) — never silent. Use this rather than declaring hundreds of one-off tokens to silence a docs page. Build-output directories (`.next`, `dist`, `coverage`, …) are skipped automatically and need no entry.

The judgment half stays with you — check what regex cannot:

| ID | Check | What to Verify | Violation |
|----|-------|---------------|-----------|
| P20 | **Layout conformance** | Diff the changed screen's structure against the contract's canonical reference file | New compositions on covered surfaces need a flagged deviation |
| P21 | **Signature technique coverage** | Every applicable surface carries the contract's signature visual technique | A new surface missing it reads instantly as inconsistent |
| P22 | **Selection semantics** | Re-click and background-click behavior on pick-one/pick-many groups matches the contract | Required selections must never empty by accident |
| P23 | **Empty states** | Collection screens have the contract's empty-state copy | Empty states are never silent |
| P24 | **App-shell protocol** | Screens report footer/overlay state per the contract; stable refs for handlers | Double-button bugs and footer teardown loops |

Severity: P12 and P22 are **error**; P1, P5, P7, P20, P21, P24 are **warning**; P23 is **info**.

## Output Format (Required, Lint)

You **MUST** structure every lint report using exactly this format. No prose summaries, no narrative explanations, no skipping the Passed or Not Applicable sections.

```
## Design Lint Report

**Scope:** [files checked, e.g. `components/**/*.tsx (12 files)`]
**Rules active:** [count of checks run]
**Result:** [X passed, Y failed, Z warnings, N not applicable]

---

### Violations

#### [A3] Missing reduced motion — **error**
- **File:** `components/Modal.tsx:28`
- **Found:** `<motion.div animate={{ opacity: 1 }}>` with no reduced-motion handling
- **Fix:** Add `const shouldReduceMotion = useReducedMotion()` and pass `initial={shouldReduceMotion ? false : { opacity: 0 }}`

#### [V1] No pure black shadows — **warning**
- **File:** `components/Card.tsx:14`
- **Found:** `box-shadow: 0 2px 8px rgba(0,0,0,0.15)`
- **Fix:** `box-shadow: 0 2px 8px rgba(15,23,42,0.15)` (tinted dark blue)

(repeat one block per violation, ordered by severity: error → warning → info)

---

### Passed
- [T2] Font display swap
- [I1] Cursor pointer (global rule exists)
- [A5] Exit animations

---

### Not Applicable
- [R1] Viewport zoom — no `<meta>` tag in scope
- [C3] Dark mode coverage — project does not declare dark mode support
```

### Wrong format (never do this)

Do not summarize violations into prose:

> "I noticed a few issues. The shadows could use some work, and there's an animation that doesn't handle reduced motion. The typography is mostly fine."

Do not collapse into a single bullet list without rule IDs and file/line references:

> - Black shadows in some files
> - Missing reduced motion handling
> - Font is OK

Do not omit the rule ID, file, line, found-snippet, or fix-snippet from any violation entry. Every violation block must have all five.

### Severity Tags (Required Per Violation)

Every violation entry **MUST** include a severity tag — `**error**`, `**warning**`, or `**info**` — on the heading line:

1. **error** — Accessibility or interaction failures (I2, I3, W1, R1, R3, A3)
2. **warning** — Design quality violations (V1-V7, T1-T4, C1-C3, A1-A2, A4-A5, I1, W3, R2)
3. **info** — Opportunities for improvement (W2, W4, I4)

Order violation entries within the report: all errors first, then warnings, then info.

## Voice (Lint)

The lint report is factual and direct. No opinions — just patterns matched or not matched. Quote the exact code. Name the exact file and line. Provide the exact fix.

"Found `rgba(0,0,0,0.15)` at `Card.tsx:14`" — not "the shadow could use improvement."

When everything passes, say so clearly: "All 28 checks passed. No violations found."

## Running Lint Automatically

This skill is designed to chain after `frontend-design`:

1. **Build** — `frontend-design` generates the UI
2. **Lint** — this skill validates the output
3. **Fix** — Agent fixes any violations in-place
4. **Re-lint** — Confirm fixes resolved the violations

The agent can run this chain without user intervention when `design-intent.mdc` is active — Question 8 ("Would someone say AI made this?") naturally leads to verification.

## Extending the Checks

To add new lint checks:

1. Identify a concrete, searchable pattern from a rule
2. Add it to the appropriate category table above
3. Include: ID, check name, what to search for, violation description
4. Ensure the check is objective — another agent running the same check on the same code should get the same result

Checks must be deterministic. "The spacing feels off" is not a check. "Spacing value `37px` does not appear in the 4/8/12/16/24/32/48/64 scale" is a check.

---

# PART 2 — SUBJECTIVE CEILING (Critique)

A systematic interface critique. Analyzes UI screenshots or component code and delivers specific, actionable feedback organized by visual design, interface design, interaction consistency, and user context. Has three output modes: the default 7-section critique, a formal [Nielsen Heuristic Mode](#nielsen-heuristic-mode) for explicit usability-audit requests, and a [Scored Mode](#scored-mode-the-gate) that turns the ceiling into a calibrated gate for the build loop.

## Input Modes

### 1. Image / Screenshot (Primary)
The user pastes or attaches a screenshot. Read the image with the Read tool, then critique what you see.

### 2. File Path (Secondary)
The user provides a component file path. Read the file, mentally render the layout from the JSX/TSX, and critique the structural and stylistic decisions in the code. Note: you cannot see the rendered output, so focus on what's inferrable — layout structure, spacing patterns, color choices, typography, hierarchy, component organization, and interaction patterns.

### 3. Live URL (Tertiary)
The user provides a URL. Use WebFetch to retrieve the page content, then critique based on the markup and any screenshots the user provides.

## Scored Mode (the gate)

The default critique (below) is *advisory* — prose feedback, never blocks. **Scored Mode** turns the ceiling into a numeric gate for the build loop, but a self-checking one: it only blocks when it has proven, this run, that it can tell good design from slop. Use it when a `VISION.md` goal is verified by `agent:` (a taste check), or when the user wants a pass/fail design-quality verdict rather than just feedback.

**Protocol:**

1. **Render the real pixels.** A taste score read off code grades *imagined* output. Score an actual screenshot of the **rendered** screen. The package ships no browser — the host renders it (screenshot the running UI or the URL). If you only have code, stay in the advisory critique below; do not gate.
2. **Score against the rubric.** Score the screen 0–1 on the four weighted axes (design, craft, originality, functionality) with a required justification paragraph, per [the scored rubric](references/rubric.md). No bare numbers.
3. **Calibrate before you gate.** Score the calibration corpus (`evals/calibration/manifest.json`) — its reference-good and reference-slop screens — the same way, blind to each anchor's expected band. Run the result through `lib/calibration.mjs` `checkCalibration(scores, anchors)`: the worst good anchor must beat the best slop anchor by the margin (0.15).
4. **Gate or advise.**
   - **Calibrated** → the screen's score may gate: below the project threshold = fail. A failed `agent:` goal is still human-promoted (`design-fleet verify` never auto-promotes), exactly like the deterministic gates.
   - **Not calibrated** → the score is **advisory only**. Report it, but the deterministic lint floor stays the sole gate. A judge that can't separate known-good from known-slop *today* has not earned the right to block.

The floor (Part 1) gates unconditionally. The ceiling gates *only while calibrated* — that asymmetry is deliberate: never let an unproven subjective judge fail a build.

### Worked example (host orchestration)

The package ships only the *decision* (`checkCalibration`, pure + zero-dep). The
host supplies the two side-effecting halves — `render` (screenshot a URL/path,
host-side) and `judge` (score a screenshot 0–1 against `references/rubric.md` with
an independent model). The loop wires them together:

```js
import { readFileSync } from "node:fs";
import { checkCalibration } from "design-fleet/lib/calibration.mjs";

const manifest = JSON.parse(
  readFileSync("skills/design-review/evals/calibration/manifest.json", "utf8")
);

// 1+2. Score every calibration anchor, blind to its expected band.
const scores = {};
for (const a of manifest.anchors) {
  scores[a.id] = await judge(await render(a.source)); // 0–1, rubric-weighted
}

// 3. Did the judge earn the right to gate THIS run?
const cal = checkCalibration(scores, manifest.anchors, { margin: manifest.margin });

// 4. Score the real screen the same way, then gate or advise.
const screenScore = await judge(await render(targetUrl));
if (cal.calibrated) {
  const THRESHOLD = 0.7;                       // the project's bar for an `agent:` goal
  if (screenScore < THRESHOLD) reportFail(`design ${screenScore.toFixed(2)} < ${THRESHOLD}`);
} else {
  reportAdvisory(`${screenScore.toFixed(2)} — ${cal.reason}`); // floor stays the only gate
}
```

`checkCalibration` refuses (advisory) if any anchor is missing a numeric score —
so a render that silently failed can never be mistaken for a passing gate.

## Critique Methodology

Follow this sequence. Do NOT skip steps or merge them. Each section should feel like its own focused lens.

### Step 0: Context

Before critiquing, briefly establish:
- **What is this?** (app type, screen purpose, target user)
- **What emotional context surrounds this task?** (stressful? casual? high-stakes? routine?)

This matters. A divorce filing app demands different care than a podcast player. Name the context so the critique respects it.

### Step 1: First Impressions

Spend one paragraph on gut reaction. What stands out? What feels off? What's the overall impression? Be honest and direct — not tentative.

This is the "noticing" step. The skill of seeing what's actually in front of you, not what you expect to see.

### Step 2: Visual Design

Audit these specific dimensions:

| Dimension | What to Look For |
| --- | --- |
| **Color intentionality** | Is every color used with purpose? Or are colors applied as decoration without meaning? Look for: too many background colors, competing accents, colors that don't establish hierarchy. |
| **Typographic hierarchy** | Is there a clear scale from most important to least? Count the distinct sizes/weights. Are headlines distinguished from body from labels? Is there unnecessary repetition of type styles? |
| **Shadow & stroke quality** | Are shadows crisp or muddy? Are strokes too prominent, competing with content? Do outlines/borders add structure or noise? |
| **Visual weight vs. importance** | Do the visually heaviest elements match what's semantically most important? Or do decorative elements steal attention from primary actions? |
| **Spacing & alignment** | Is spacing consistent? Are elements aligned to a clear grid? Is there excess padding pushing content away from where it should be? |
| **Icon consistency** | Are icons from the same family? Same weight/stroke width? Same optical size? Or is it a mix of styles? |

For each issue found, use this structure:
> **[Issue name]** — [Specific factual observation]. [Impact on user or experience]. [What it could be instead.]

Be precise. Count things. Quote text. Name colors. Measure relative sizes. "There are four distinct background colors competing for attention" is better than "too many colors."

### Step 3: Interface Design

Audit these dimensions:

| Dimension | What to Look For |
| --- | --- |
| **Focusing mechanism** | Is it clear where the user should look first? Is there a visual entry point? Or does everything compete for attention equally? |
| **Progressive disclosure** | Is complexity revealed gradually, or is everything dumped on the user at once? Are there 40 things on screen when 5 would suffice? |
| **Information density** | Is the density appropriate for the context? Data dashboards can be dense; onboarding should not be. |
| **Expectation setting** | Does the user know what will happen next? Is progress communicated? Is scope clear? |
| **Feedback & reward** | Does the interface acknowledge user actions? Are completed items celebrated or just checked off? Is there a sense of forward momentum? |
| **Redundancy** | Are labels, titles, or descriptions repeating information the user already knows? Can anything be removed without losing meaning? |

Frame issues as missed opportunities:
> "We're missing an opportunity to [reward progress / reduce cognitive load / set expectations / etc.]"

### Step 4: Consistency & Conventions

| Dimension | What to Look For |
| --- | --- |
| **Pattern consistency** | Are similar actions handled the same way throughout? Or do interaction patterns shift without reason? |
| **Platform conventions** | Does the design follow established platform patterns (iOS, Android, web)? Deviations should be intentional improvements, not accidents. |
| **Component reuse** | Are there elements that look like they should be the same component but aren't? Inconsistent card styles, button treatments, list items? |
| **Visual language cohesion** | Does the interface feel like one designer made it? Or does it feel assembled from different kits? |

### Step 5: User Context

This is where empathy meets analysis:

- **How does this design make the user feel?** Name the emotion. "Overwhelmed," "confused," "unsupported," "rushed."
- **What is the user's likely state of mind?** Anxious? Focused? Browsing casually? Under time pressure?
- **Does the interface respect that state?** Or does it add unnecessary cognitive burden?
- **What would "uncommon care" look like here?** What would surprise the user with thoughtfulness?

### Step 6: Refinement Pass

A deliberate final sweep for the small, high-craft details the broad lenses
above tend to skip — the difference between "fine" and "considered." A fast
critique reliably misses these, so check each one explicitly:

| Check | The miss it catches |
| --- | --- |
| **Same-type controls match** | Icon buttons of the same kind should share one container, size, and hit area. A bare back chevron next to a `…` button in a circular chip is inconsistent — give both the same treatment. |
| **The hero value has a home** | The single most important number/stat should sit in its own container/surface, not float unemphasized while everything around it aligns differently. |
| **Row elements share one centerline** | In a row (icon + text + trailing control/avatar), everything should sit optically centered on one horizontal axis, not align to different heights. |
| **Off/unselected states stay quiet** | Unselected checkboxes/toggles/rows shouldn't be visually loud (heavy borders). The "off" state recedes; selection controls match the product's shape language (circular vs square). |
| **Equal segments, uniform inset** | Segmented controls get equal-width segments and a selected chip inset evenly on all sides. |
| **Optical padding around glyphs** | A trailing glyph with built-in whitespace (▶, chevrons) needs its padding trimmed so it looks balanced, not ruler-equal. |
| **Dividers inset to text** | List separators start at the text column, not full-width under the leading icons. |
| **Concentric radii** | Nested rounded elements — inner and outer corner radii should feel related, not arbitrary. |
| **Optical over mathematical** | When equal numbers look unequal, adjust to what the eye reads as balanced. |

Run these as an explicit checklist; do not assume the lenses above already
covered them.

## Output Format (Required, Default Critique Mode)

This is the default critique mode. The only exception is an explicit heuristic-review request — see [Nielsen Heuristic Mode](#nielsen-heuristic-mode) below.

You **MUST** structure every critique using exactly these seven H2 sections, in this order, with no extras and no omissions:

```
## Context
[1-2 sentences on what this is and who it's for]

## First Impressions
[1 paragraph, direct and honest, no hedging]

## Visual Design
[Each issue as: **Issue Name** — observation. Impact. Opportunity.]

## Interface Design
[Each issue framed as a missed opportunity]

## Consistency & Conventions
[Pattern and convention issues, same Issue Name format]

## User Context
[Empathy-driven observations, named emotion + impact on the user's state]

## Top Opportunities
[Ranked list of the 3-5 highest-impact changes, each in one sentence, ordered by impact (structural > behavioral > visual)]
```

### Wrong format (never do this)

Do not collapse sections into a single prose blob:

> "The design has some color issues and the typography hierarchy could be clearer. Also the spacing feels inconsistent. Overall it's pretty solid but could use polish."

Do not use bullets without the **Issue Name** header pattern:

> - Too many colors competing
> - Hierarchy unclear
> - Spacing off

Do not start every issue with "I think" or "It might be":

> - I think the shadows could be a bit muddier than they need to be
> - It might be worth considering whether the buttons feel responsive

### Correct format (per issue, inside each section)

> **Muddy shadows** — Card shadows use 24px blur at 8% opacity, creating a hazy halo rather than crisp depth. This makes the cards feel like they're floating in fog instead of sitting on a surface. Tighter shadow (8px blur, 16% opacity, with a 1px directional offset) would give the layout cleaner elevation.

The Issue Name is **bold**, followed by an em dash, followed by three concrete sentences: factual observation (with numbers/colors/sizes when possible) → impact on the user → specific opportunity. No hedging language.

If a section has no issues worth raising, write the section header followed by a single line: "Nothing notable in this dimension." Never omit the section.

## Nielsen Heuristic Mode

A second, formal output mode. Switch to it ONLY when the user explicitly asks for a Nielsen review, a heuristic review or heuristic evaluation, a formal usability audit, or a pass against "the 10 heuristics". A plain "critique" or "review" stays in the default 7-section format above.

**This mode REPLACES the 7-section format. The two formats never mix in one response.** A response is either a standard critique or a heuristic evaluation — decide before writing, never blend.

Full per-heuristic detail — what to check, implementation patterns, severity examples — lives in the [Nielsen heuristics reference](references/nielsen-heuristics.md). Read it before running this mode. The **Common Violations in CMS/Admin UIs** table also lives in that reference.

### The 10 Heuristics

| # | Heuristic | Core question |
| --- | --- | --- |
| H1 | Visibility of system status | Does the UI show what's happening — loading, saving, success, error? |
| H2 | Match with the real world | Does it use the user's language, not dev jargon? Are icons and metaphors intuitive? |
| H3 | User control and freedom | Can users undo, cancel, go back, or escape from any state? |
| H4 | Consistency and standards | Does it follow the app's own patterns and platform conventions? |
| H5 | Error prevention | Are destructive actions guarded? Are constraints in place before errors happen? |
| H6 | Recognition over recall | Are options visible? Can users see rather than remember what to do? |
| H7 | Flexibility and efficiency | Are there shortcuts for experts? Does it scale for power users? |
| H8 | Aesthetic and minimalist design | Is every element earning its place? Is the signal-to-noise ratio high? |
| H9 | Error recovery | Are error messages plain language, specific, and actionable? |
| H10 | Help and documentation | Is contextual help available where users might need it? |

### Severity Vocabulary (Required)

Rate every violation with exactly one of these four words. No other severity terms in this mode.

| Severity | Meaning |
| --- | --- |
| **cosmetic** | Noticeable but does not impede the task. Fix if time allows. |
| **minor** | Causes hesitation or mild irritation; users recover quickly. Low priority. |
| **major** | Causes significant confusion, inefficiency, or task failure for some users. High priority. |
| **catastrophic** | Blocks task completion or causes data loss. Must fix before release. |

### Output Format (Heuristic Mode)

Walk all 10 heuristics in order, H1 through H10 — never skip or reorder. For each:

```
### H<N>. <Heuristic name>
- **[severity]** <Specific violation — what, where, and the fix.>
```

A heuristic with no violations gets a single line: "No violations found." Never omit a heuristic — the discipline of this mode is exhaustiveness, not selectivity. Close with a **Summary** section: all violations ranked catastrophic → major → minor → cosmetic, one line each, so the user knows what to fix first.

The Voice Rules below apply in this mode too: specific, decisive, factual-first, no hedging.

### How to Apply

**Reviewing existing UI:** Walk through each user flow as if seeing it for the first time. Map each issue to the single heuristic it most violates, rate it, and propose the fix in the same breath.

**Building new UI (proactive):** Scan the checklist before implementation to anticipate issues. Prioritize H1 (status), H3 (control), and H5 (error prevention) — these prevent the most user frustration. After implementation, do a final pass against all 10.

### Common Violations in CMS/Admin UIs

This table lives in the [Nielsen heuristics reference](references/nielsen-heuristics.md#common-violations-in-cmsadmin-uis) — pattern → heuristic violated → fix, covering missing save/delete feedback (H1), unvalidated form submits (H5), no mid-flow cancel (H3), inconsistent button placement (H4), raw error codes (H9), unguided empty states (H10), truncated text without expansion (H6), delete without confirmation (H5), missing async loading indicators (H1), and developer jargon in labels (H2). Consult it when reviewing CMS/admin screens.

## Voice Rules

These define how the critique sounds — in both critique modes. Follow them strictly.

### BE:
- **Specific** — "There are six columns of data per row" not "there's a lot of data"
- **Decisive** — "This is overwhelming" not "this might feel overwhelming"
- **Factual first** — State what you see before judging it. Count elements, name colors, measure sizes. The specificity of the observation is the quality of the critique.
- **Impact-aware** — Connect every observation to how it affects the user
- **Constructive** — Every problem gets an opportunity. Never just tear down.
- **Quantitative** — Count elements, name colors, measure relative sizes
- **Warm but direct** — A builder reviewing work with a builder they respect. Honest because the goal is to make the work great.
- **Simple in language** — Everyday vocabulary. No academic jargon. "Progressive disclosure" is fine. "The affordance signifiers lack semiotic clarity" is not.

### DO NOT:
- **Hedge** — No "maybe," "perhaps," "it could be argued that"
- **Apologize** — No "unfortunately" or "sadly"
- **Be vague** — No "the design feels off" without saying exactly what and why
- **Prescribe without reasoning** — Never say "change X to Y" without explaining the why
- **Add praise padding** — Don't sandwich criticism with empty compliments. If something works well, say so specifically. But don't manufacture positivity.
- **Use jargon without explanation** — Keep it accessible
- **Use life-coach energy** — No "10x your design" or universal truths. Stay grounded and real.

### Tone
Direct, analytical, and honest — rooted in wanting the work to be great. No cruelty, no condescension, no hand-holding. Short blunt sentences when something is clearly wrong. Flowing when exploring nuance. Use the em dash to land a point or add context before a verdict.

## Severity Guide

Not all issues are equal. When listing issues, implicitly order by impact:

1. **Structural** — Problems with information architecture, missing functionality, wrong mental model. These change what the interface *is*.
2. **Behavioral** — Problems with how the interface responds, flows, or communicates. These change how the interface *feels*.
3. **Visual** — Problems with color, type, spacing, shadows. These change how the interface *looks*.

A structural issue (wrong mental model for a divorce app) matters more than a visual one (shadow is slightly muddy). Prioritize accordingly.

## Frameworks to Reference

These are the conceptual tools behind the critique. You don't need to name them explicitly, but they should inform your analysis:

### Noticing
The foundation. Most people glance; good designers see. Count the elements. Name the colors. Measure the spacing. The specificity of your observation is the quality of your critique.

### Industry Standards
Every popular app in a category sets an invisible bar. Users carry those expectations into every new app. A notes app is compared to Apple Notes, Bear, Notion. A dashboard is compared to Stripe, Linear, Vercel. If the design falls below the bar users already carry, it feels amateur — even if the user can't articulate why.

Ask: "What would the best-in-class app in this category do here?"

### Facets of Quality
Quality isn't one thing. It's a collection of attributes. For any given interface, identify which 3-5 attributes matter most. Not every app needs to be "playful" — but every app needs to be clear about what it values and execute on those values consistently.

### Uncommon Care
The difference between good and great is often invisible to people who haven't seen great. Look for moments where the designer could have gone further — micro-interactions, empty states, error messages, transitions, loading states, edge cases. These are where "uncommon care" lives.

### Separation of Concerns
Visual design, interface design, and interaction design are different skills solving different problems. A beautiful interface can be unusable. A usable interface can be ugly. Critique each dimension on its own terms before synthesizing.

## Examples of Good Critique Language

**Visual:**
> **Muddy shadows** — The card shadows use a large blur radius with low opacity, creating a hazy, unfocused look rather than crisp depth. This makes the cards feel like they're floating in fog rather than sitting on a surface. Tighter, more directional shadows would give the layout a cleaner sense of elevation.

**Interface:**
> **No focusing mechanism** — All four content areas compete equally for attention. There's no visual entry point — no element says "start here." The user's eye bounces between the sidebar, the header stats, the chart, and the table with no clear priority. A stronger size or weight differential on the primary content area would give the layout a clear narrative.

**User context:**
> **Demoralizing progress display** — Showing "10 / 47 tasks complete" immediately communicates that 37 tasks remain. For a process that takes weeks and involves one of the most stressful experiences in a person's life, this is demoralizing. "Complete Phase 1 of 4" is psychologically very different — it frames the same progress as achievable milestones rather than an endless checklist.

**Opportunity:**
> We're missing a huge opportunity to reward progress. Completed steps could collapse or fade, making the remaining work feel smaller — not larger — as the user advances.
