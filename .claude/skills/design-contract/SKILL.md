---
name: design-contract
description: Owns the project's design contract end to end, in five modes. Setup walks the user through a taste questionnaire (aesthetic, reference products, anti-references) and writes a persistent design-taste profile (runs once per project/user). Assess audits a codebase for design consistency and produces a rated migration report. Draft generates a filled VISION.md at the project root (north star, ALWAYS/NEVER rules, tokens, type ramp, error strings, timings, examples) from what code reveals plus an interview. Reconcile diffs an existing VISION.md against the code and flags staleness. Plan turns the FEEDBACK.md inbox and north star into proposed Goals rows. Use to set up a taste profile, audit design quality or debt, create a design vision or contract, define hard rails, verify VISION.md matches reality, or process feedback into goals. Works greenfield. Not for critiquing screenshots or Nielsen reviews (design-review), building UI (frontend-design), or verifying just-generated code (design-review).
argument-hint: "[setup | assess | draft | reconcile | plan] [project path or 'this project']"
---

# Design Contract

Own the project's design contract end to end — from the user's persistent taste profile to the normative hard-rails `VISION.md` that makes every change look like the same hand drew it. Five modes: **setup** (establish the taste profile), **assess** (rated audit of what exists), **draft** (write the contract), **reconcile** (check the contract against reality), **plan** (turn feedback and the north star into proposed goals).

---

## Mode Selection

Decide the mode before doing anything else, in this order:

| Signal | Mode |
|--------|------|
| First time Design Fleet is installed, the `design-intent.mdc` rule detects no `design-taste.mdc` exists, or the user asks to set up / configure / regenerate their design taste profile, or create a personal design profile | **setup** |
| User says "reconcile", "is the vision stale", "does VISION.md match the code" | **reconcile** |
| `VISION.md` exists at the project root and the user re-runs this skill without a more specific ask | **reconcile** (the default for re-runs) |
| User says "audit", "assess", "evaluate", "design debt", "how consistent is this codebase", "what needs fixing" | **assess** |
| User says "create a vision", "design contract", "project design rules", "hard rails", "draft the vision" — or accepts the offer at the end of assess | **draft** |
| User says "plan", "propose goals", "what should we build next", "process the feedback" — or asks to turn FEEDBACK.md into goals | **plan** |
| `VISION.md` already exists and the user asks to draft | Confirm explicitly before overwriting — a full re-draft destroys hand-tuned rules. Offer reconcile first. |

Setup is run-once setup, not daily work — see the run-once gate in **Mode: Setup**. The other four modes are the recurring design-contract work.

**Not this skill:** Critiquing a screenshot or mockup, or running a formal Nielsen heuristic checklist — use `design-review`. Building new UI — use `frontend-design`. Verifying just-generated code against grep-able rules — use `design-review`. Asking how to implement a motion, easing, or CSS/SVG pattern — use `frontend-design`.

**Precedence (state this in every artifact you produce):** `VISION.md` > `design-taste` profile > package defaults. The package's built-in rules fill VISION's silences — accessibility floors, reduced-motion handling, OKLCH guidance all still apply where VISION.md says nothing. VISION.md never needs to repeat them.

---

## Mode: Setup

Establish a persistent design taste profile by asking the user structured questions about their aesthetic preferences. The output is a `design-taste.mdc` rule file that guides every future design decision. This mode runs **once** per project or per user — it's setup, not daily work.

### Initial Response (Required Gate)

When this mode is first invoked, **do not** immediately launch the questionnaire. The agent's selector may have over-matched on a related phrase, or the user may already have a profile they don't want to regenerate. Confirm intent first.

Respond with **exactly** this message and then stop:

> Ready to set up your Design Fleet taste profile.
>
> This is a short structured conversation (about 12 questions) covering aesthetic preferences, reference products you admire, and anti-references you want to avoid. The output is a `design-taste.mdc` rule that guides every future UI decision in this project.
>
> Want to begin? Reply **yes** to start, **no** to cancel, or **sample** to see one example question first.

Wait for the user's reply. Only continue past this gate if they say yes (or equivalent confirmation). If they reply "sample", show them one example question from Step 2's questionnaire and re-ask. If they reply "no" or anything ambiguous, stop and let them redirect.

If `design-taste.mdc` already exists in the project, also mention that fact in the gate message and ask whether they want to regenerate it.

### When to Use Setup

- First time Design Fleet is installed in a project
- The `design-intent.mdc` rule detects no `design-taste.mdc` exists
- User explicitly asks to set up, configure, or regenerate their design taste profile
- User wants to create a personal design profile (user-level, not project-level)

This mode runs once — it's setup, not daily work. If the user already has a taste profile and wants to audit existing code or draft a project VISION.md contract, use **assess** / **draft** instead.

### Onboarding Process

Follow this sequence exactly. Do not skip the questionnaire. Do not assume answers.

#### Step 1: Determine Scope

Ask the user: is this a **project-level** profile (lives in the project's `.cursor/rules/`) or a **personal** profile (lives in `~/.cursor/rules/` and applies to all projects)?

- **Project-level**: The taste profile describes this specific product's design direction
- **Personal**: The taste profile describes the user's general aesthetic preferences as a baseline for all projects

#### Step 2: Ask the Questionnaire

Ask the user one question at a time, waiting for an answer before moving to the next. If the host environment exposes a structured question tool (Cursor's `AskQuestion`, Claude Code's interactive prompts), use it; otherwise present each question as a plain message in the conversation. Do not paste all questions at once and do not move to Group 2 until Group 1 is fully answered.

##### Group 1: Aesthetic Direction

**Question 1 — Aesthetic gravity**

Which aesthetic directions do you gravitate toward? (allow multiple)

- Minimal and precise (Linear, iA Writer, Things)
- Editorial and expressive (The Verge, Bloomberg, Medium)
- Dense and data-heavy (Stripe Dashboard, Grafana, Bloomberg Terminal)
- Warm and approachable (Airbnb, Notion, Slack)
- Bold and brutalist (Craigslist-intentional, raw structure, exposed grid)
- Luxury and refined (Apple, Aesop, Squarespace)
- Retro or nostalgic (pixel art, skeuomorphic, vintage web)
- No strong preference — surprise me each time

**Question 2 — Color mode**

What's your default color mode?

- Dark-first (light mode secondary)
- Light-first (dark mode secondary)
- Both equally important
- Depends on the project

**Question 3 — Density**

How dense should interfaces feel by default?

- Dense — I want information, not whitespace. Efficiency over aesthetics.
- Balanced — breathe where it helps, dense where it matters
- Spacious — I prefer guided, generous layouts with room to think

**Question 4 — Animation philosophy**

How do you feel about motion in interfaces?

- Almost none — speed over smoothness, instant everything
- Purposeful moments — transitions where they serve comprehension
- Rich and expressive — animation is part of the experience

##### Group 2: Specifics

After the user answers Group 1, ask Group 2. These are free-text questions — present them conversationally, not as multiple choice.

**Question 5 — Reference products**

Ask: "Name 2-3 products or apps whose design you admire. What specifically do you like about each?"

Listen for: typography choices, color sensibility, density, motion, layout patterns, tone.

**Question 6 — Anti-references**

Ask: "Any products whose aesthetic you actively dislike? What bothers you about them?"

Listen for: what the user considers generic, noisy, childish, sterile, or overwhelming. These anti-references are as valuable as the positive ones.

**Question 7 — Typography taste**

Ask: "Do you have font preferences? Any fonts you love, or any you never want to see?"

If the user doesn't have strong opinions, note that and leave the typography section open in the profile.

**Question 8 — Signature detail**

Ask: "When you see an interface you love, what's the detail that makes you pause? What separates 'good' from 'this was made by someone who cares'?"

This question reveals what the user values most — it might be typography, micro-interactions, density, whitespace, color restraint, or something unexpected.

#### Step 3: Check for Existing Code (project-level only)

If this is a project-level profile and the project already has UI code:

1. Run a quick scan for the design surface (globals.css, tailwind config, theme files, component library)
2. Note what already exists — fonts in use, color variables, spacing patterns
3. Include these in the profile under "Current Project DNA" so the agent extends rather than fights the existing system

#### Step 4: Generate the Taste Profile

Using the questionnaire answers, generate a `design-taste.mdc` file. Follow this template — adapt the content to the user's actual answers, don't copy the template literally.

```markdown
---
description: Design taste profile for [user name or project name]. Guides aesthetic decisions across all UI work.
globs: "**/*.{tsx,jsx,vue,svelte,css,scss,html}"
alwaysApply: true
---

# Design Taste Profile

## Aesthetic Direction
- Gravitates toward: [from Q1 — e.g. "dense, editorial, precise"]
- Avoids: [from Q6 — e.g. "playful, rounded, bubbly, toy-like"]
- Products admired: [from Q5 — e.g. "Linear (density + keyboard-first), Stripe Dashboard (data clarity), iA Writer (typographic focus)"]
- Products disliked: [from Q6 — e.g. "Notion (too toylike at scale), Canva (visually overwhelming)"]

## Color
- Default mode: [from Q2 — e.g. "dark-first"]
- Accent approach: [inferred — e.g. "sharp and singular, no gradients"]
- Temperature: [inferred from references — e.g. "cool neutrals, blue-tinted grays"]

## Typography
- Preference: [from Q7 — e.g. "geometric sans-serifs, open to serif pairings for editorial contexts"]
- Fonts liked: [from Q7 — e.g. "Söhne, Instrument Sans, Newsreader"]
- Fonts to avoid: [from Q7 or inferred — e.g. "Inter, Roboto, system defaults"]

## Density & Space
- Default density: [from Q3 — e.g. "dense — information over whitespace"]
- Whitespace philosophy: [inferred — e.g. "earned, not default — generous only on marketing/onboarding pages"]

## Motion
- Philosophy: [from Q4 — e.g. "almost none — speed over smoothness"]
- Acceptable motion: [inferred — e.g. "page transitions under 200ms, no hover animations on frequently-used elements"]
- Never: [inferred from anti-references — e.g. "bounce, elastic easing, decorative loading animations"]

## The Signature Detail
- What separates good from great: [from Q8 — e.g. "typographic precision — proper numeric formatting, considered line-height, text-wrap: balance on every heading"]

## Current Project DNA (if applicable)
- Fonts in use: [from Step 3 scan]
- Color system: [from Step 3 scan]
- Component library: [from Step 3 scan]
- Patterns to preserve: [from Step 3 scan]
```

#### Step 5: Confirm and Install

1. Show the generated profile to the user
2. Ask if anything needs adjustment
3. Install the file for every target present in the project — check which install dirs exist and write to each:
   - **Cursor, project-level**: `.cursor/rules/design-taste.mdc`
   - **Cursor, personal**: `~/.cursor/rules/design-taste.mdc`
   - **Claude Code, project-level**: `.claude/rules/design-taste.md` — same body, strip the frontmatter block entirely (a Claude rule with no `paths:` field loads at launch)
   - **Claude Code, personal**: `~/.claude/rules/design-taste.md`
4. Confirm installation: "Your taste profile is set. Every UI decision the agent makes will now run through it."

### After Onboarding

Once the taste profile exists:

- `design-intent.mdc` reads it automatically (it's an always-on rule in the same directory)
- Question 1 ("What one word?") and Question 4 ("What's the reference?") from design-intent now have defaults from the profile
- The `frontend-design` skill uses the profile to guide font selection, color choices, and density decisions
- The profile can be updated anytime — it's just a `.mdc` file the user can edit directly

The taste profile is a living document. Encourage the user to refine it as their preferences evolve or as the project's direction sharpens.

---

## Mode: Assess

Scan an existing codebase, extract its design profile, rate each dimension, and produce a prioritized migration report. Follow this sequence exactly — each step feeds the next.

### Step 1: Locate the Design Surface

Find the files that define the project's visual language:

```
Search for (in order):
1. globals.css, global.css, app.css, index.css — global styles
2. tailwind.config.js, tailwind.config.ts — Tailwind theme
3. theme.ts, theme.js, tokens.ts, tokens.js — design tokens
4. components/ui/ — shared UI component library
5. layout.tsx, layout.jsx — root layout (fonts, providers)
6. package.json — check for styling/animation dependencies
```

Read each file found. These are the source of truth for what the project considers its design system.

### Step 2: Extract the Design Profile

From the files found, extract concrete values. Be specific — count things, name things, quote values.

#### 2a. Typography

| Question | How to Find |
|----------|-------------|
| What font families are in use? | Search for `font-family`, `--font-`, `next/font`, `@font-face`, Google Fonts imports |
| How many distinct font sizes? | Search for `font-size`, `text-{size}` Tailwind classes, `--font-size-` variables |
| How many font weights? | Search for `font-weight`, `font-{weight}` classes |
| Is there a type scale? | Check if sizes follow a ratio or are arbitrary |
| Is `font-display: swap` used? | Check `@font-face` declarations and `next/font` config |
| Is antialiasing set? | Search for `-webkit-font-smoothing` |

#### 2b. Colors

| Question | How to Find |
|----------|-------------|
| How many unique color values? | Search for hex codes (`#[0-9a-fA-F]{3,8}`), `rgb()`, `hsl()`, Tailwind color classes |
| How many are in CSS variables? | Count `--color-` or `--{name}` variables in globals |
| How many are hardcoded? | Count hex/rgb values in component files (not in globals or config) |
| Is there a palette? | Check Tailwind config `colors` or CSS variable definitions |
| Are alpha colors used for borders? | Search for `rgba`, `hsla`, `/0.` patterns on borders |

#### 2c. Spacing

| Question | How to Find |
|----------|-------------|
| What spacing values are used? | Search for `padding`, `margin`, `gap`, `p-`, `m-`, `gap-` |
| Do they follow a scale? | Check if values are multiples of 4 (4, 8, 12, 16, 24, 32, 48) |
| Are there arbitrary values? | Look for odd numbers or non-scale values like `13px`, `37px`, `p-[23px]` |

#### 2d. Shadows & Borders

| Question | How to Find |
|----------|-------------|
| How many shadow definitions? | Search for `box-shadow`, `shadow-`, `--shadow` |
| Are shadows layered? | Check if any shadow uses multiple comma-separated values |
| Are shadows consistent direction? | Compare offset-x/offset-y across shadows |
| Are border colors hardcoded or variable? | Search for `border-color`, `border-{color}` |

#### 2e. Animation & Motion

| Question | How to Find |
|----------|-------------|
| What animation library is used? | Check package.json for framer-motion, motion, react-spring, gsap |
| What durations are in use? | Search for `duration`, `transition:`, `animation:`, timing values |
| What easings are in use? | Search for `ease`, `cubic-bezier`, `ease-in`, `ease-out`, `ease-in-out` |
| Is `prefers-reduced-motion` respected? | Search for `prefers-reduced-motion`, `useReducedMotion` |
| Are `exit` animations present? | Search for `AnimatePresence`, `exit=` |

#### 2f. Components

| Question | How to Find |
|----------|-------------|
| Is there a shared UI library? | Check for `components/ui/`, `@your-org/ui`, shadcn components |
| How many button variants exist? | Search for button components, count distinct styles |
| Are inputs/forms consistent? | Compare form components across features |
| Is `cn()` or `clsx` used? | Search for the class merging utility |

#### 2g. Keyboard & Interaction

| Question | How to Find |
|----------|-------------|
| Are keyboard shortcuts defined? | Search for `useHotkeys`, `addEventListener('keydown'`, `Cmd+`, `Ctrl+` |
| Does Escape close overlays? | Search for `Escape` key handling in modals/dialogs |
| Is focus management handled? | Search for `focus-visible`, `FocusTrap`, `autoFocus` |
| Are hover states touch-safe? | Search for `@media (hover: hover)`, `pointer: fine` |

### Step 3: Rate Each Dimension

| Rating | Meaning |
|--------|---------|
| **Solid** | Consistent, follows a system, matches or exceeds Design Fleet standards |
| **Partial** | Has a system but inconsistently applied, or the system has gaps |
| **Missing** | No system, arbitrary values, no consistency |

### Step 4: Produce the Audit Report

```
## Design Audit Report

### Project: [name] — Date: [date]
### Overall Assessment: [1-2 sentences]

### Design Profile
[One block per dimension — Typography, Colors, Spacing, Shadows & Borders,
Animation, Components, Keyboard & Interaction. Each block: the concrete
findings (counts, names, quoted values) and a Solid/Partial/Missing rating.]

### Top 5 Opportunities (by impact)
1-5. [Highest impact finding first — what to fix, where, why it matters]

### Migration Order
1. **Typography pass** — [specific actions]
2. **Color pass** — [specific actions]
3. **Spacing pass** — [specific actions]
4. **Animation pass** — [specific actions]
5. **Interaction pass** — [specific actions]
```

### Step 5: Offer Draft Mode

End every assess run with the offer — not a generated file:

> "The extraction work is done. Want me to turn it into a `VISION.md` design contract? I'd pre-fill the tokens, type ramp, spacing, and animation tables from what I just found, then ask a few questions about the things code can't reveal."

If the user accepts, switch to draft mode and reuse the Step 2 extraction — do not re-scan.

---

## Mode: Draft

Produce a filled `VISION.md` at the **project root**. The full hardened template is [vision-template.md](references/vision-template.md) — read it before writing anything.

### Step 1: Mechanical Pre-fill

Run the assess extraction (Steps 1–2 above) if it hasn't run this session, then map findings into the template:

| Template section | Pre-fill from |
|------------------|---------------|
| Lint hooks table | Tokens file path; error/toast helper function name; field-error class name; app-shell helper path; icon package name from package.json |
| Design tokens table | Token values from the canonical tokens file (colors, radii, field height) |
| Spacing table | The gap/padding values actually in use, snapped to the named roles |
| Typography table | Families, sizes/leading, weights per role from extraction 2a |
| Animation timing table | Durations and easings from extraction 2e |
| Canonical examples table | The best existing file per pattern — the one you'd tell another agent to diff against |

Pre-fill only what the code actually shows. Never invent a literal to make a table look complete.

### Step 2: Ingest the Taste Profile

If `.cursor/rules/design-taste.mdc` or `.claude/rules/design-taste.md` exists, read it. Its "Signature Detail" seeds the **Signature visual technique** section; its aesthetic direction, motion philosophy, and font preferences inform the interview defaults. Don't re-ask what the profile already answers — confirm instead ("Your taste profile says X — does that hold as a hard rule here?").

### Step 3: Gap Interview

Interview for what code cannot reveal. **One question at a time** — wait for each answer. Keep it short (5–7 questions); skip anything extraction or the taste profile already answered.

1. **North star** — problem, user, the one optimized outcome, standing constraint (demo vs production, desktop-first vs mobile-first, performance/a11y floor)
2. **ALWAYS/NEVER hard rules** — which template defaults are binding here, plus any project-specific additions (fixed sets that must not grow, commit rules, demo boundaries)
3. **Exact error strings** — the phrasing table rows; titles and descriptions verbatim
4. **App-shell protocol** — how screens report CTAs, overlay state, navigation; or "no shell" for simple apps
5. **Selection-pattern decisions** — required-selection behavior, skip affordances, selected-state recipe
6. **Signature visual technique** — confirm the taste-profile seed or capture one; "None — by decision" is a valid answer

### Step 4: Fill the Template

Fill [vision-template.md](references/vision-template.md) with the pre-fill plus interview answers. Non-negotiable mechanics — a parser reads this file:

- Keep the `<!-- qd-vision-template: v1 -->` version marker, every `<!-- qd:* -->` anchor, and every table header **byte-for-byte intact**. Never rename a column, reorder columns, or drop the Lint hooks rows.
- **Never silently drop a section.** A section the project doesn't need keeps its `[PLACEHOLDER]` values — lint then reports it as not-applicable instead of missing.
- Filled cells are backticked literals in canonical form (`#0f172a`, `8px`, `200ms`, `cubic-bezier(0.32, 0.72, 0, 1)`, `components/ui/feedback.ts`). Unfilled cells keep the `[UPPERCASE]` placeholder grammar.
- Write the result to `VISION.md` at the project root, then show the user a summary of what was filled vs left placeholder.

**Greenfield path:** no UI code → skip Step 1 entirely, run the interview only. Extraction-fed sections (tokens, spacing, animation, canonical examples, lint hooks) keep their placeholders. The contract still ships whole — it fills in as the project grows, and reconcile mode tracks the gap.

**Migration note:** if `.cursor/rules/design-context.mdc` or `.claude/rules/design-context.md` exists (output of the old design-audit skill), fold its "Migration Targets" into VISION.md's optional Migration appendix — respect the appendix's 10-bullet cap — then suggest deleting the old file. VISION.md supersedes it.

### Step 5: Wire It

`VISION.md` is inert until agents load it. Tell the user to run:

```
npx design-fleet vision --wire
```

The CLI writes both pointer artifacts idempotently. If the user prefers, write them directly instead — exactly these two:

- **`.cursor/rules/vision.mdc`** — a thin `alwaysApply: true` pointer rule: "The hard rails live in `VISION.md` at the project root. Read it before any non-trivial change." Nothing else — the rule points, the contract lives in VISION.md.
- **`CLAUDE.md`** — a marker-delimited block (so re-wiring never clobbers hand-written content) containing the same instruction plus an `@VISION.md` import.

Be target-neutral: Cursor-only installs get the `.mdc`, Claude-only installs get the `CLAUDE.md` block, dual installs get both. Detect from which config directories exist; ask if neither does.

---

## Mode: Reconcile

The staleness pass. VISION.md is a contract — when it drifts from the code, every agent reading it inherits the lie.

### Checks

Read `VISION.md`, then verify each claim against reality:

| Check | How |
|-------|-----|
| Canonical paths exist | Every path in the Canonical examples table and the two path hooks in the Lint hooks table (`tokens-path`, `app-shell-helper`) resolves to a real file |
| Identifier hooks exist | The three identifier hooks are real in the code: grep for call sites of the `error-helper` function, usages of the `field-error-class` class name, and the `icon-package` in package.json/imports |
| Tokens match | Declared token values (colors, radii, field height) appear in the canonical tokens file with the same values |
| Typography matches | Declared families/weights are the ones actually loaded and used |
| Timings match | Declared durations/easings appear in the animation code; flag undeclared durations in the code too |
| Error strings match | Phrasing-table wording appears verbatim in the code |
| Placeholders aging | Sections still all-placeholder months in — remind the user to fill or explicitly mark "None — by decision" |
| Appendix staleness | Migration appendix present but targets completed, or over the 10-bullet cap |

### Report and Resolve

For each contradiction produce a finding:

```
**Finding:** VISION.md says modal cards animate at `200ms`; `Modal.tsx:31` uses `280ms`.
**Source of truth?** (a) the doc — I'll update VISION.md to `280ms`, or
(b) the code — I'll fix Modal.tsx to `200ms`.
```

Ask which side wins **per finding** — never assume, never bulk-resolve. Propose the specific edit for whichever side the user picks; doc edits you may apply directly, code edits you propose as concrete diffs. Findings with an obvious answer (a path that was renamed in a refactor) still get confirmed, just with a recommended default.

A clean pass deserves a clear verdict: "VISION.md matches reality. N claims checked, 0 contradictions."

---

## Mode: Plan

The forward pass. Reconcile catches the contract lying about the present; plan decides what the contract should demand next. Output is **proposed rows for the Goals table** — never adopted goals, never built features.

### Inputs (read all that exist)

| Source | What it contributes |
|--------|---------------------|
| `VISION.md` North Star + Goals table | The destination, and what is already promised (all statuses — never re-propose a goal that exists) |
| `FEEDBACK.md` at the project root | The raw inbox: dated bullets of user feedback, client notes, support themes |
| Latest critique findings (a design-review report, if the user has one) | Observed UI gaps worth promoting to goals |
| `STATE.md` (when a loop maintains one) | What's in flight or escalated — don't propose what's already being built |

`FEEDBACK.md` is a plain markdown inbox anyone can append to:

```markdown
# Feedback inbox
- 2026-06-11: three support tickets say checkout is confusing
- 2026-06-12: client wants a pricing page before the launch
```

### Classify each feedback item first

Not all feedback is goal-shaped. Route each item by where the change belongs:

| Shape | Signal | Output |
|-------|--------|--------|
| **Capability** — the product should *do* something | "clients want CSV export", "add a step to onboarding" | A proposed Goals row (steps below) |
| **Contract** — the design system itself should change | "introduce a warmer accent", "buttons feel cramped", new error-copy tone | A **proposed amendment**: quote the exact VISION.md table-row diff (token row, spacing value, phrasing row) in your report. Never apply it yourself — the human applies or approves it, and only then does the new value exist for builds and lint |
| **Redesign-scale** — many contract sections at once | "full visual refresh", "rebrand" | Recommend a `design-contract` assess → draft session. Don't piecemeal a redesign through single amendments — the contract gets rewritten *with* the human, then every later edit builds to the new rails |

The contract row matters most: "use a new color" must become a token-map
amendment *before* any build uses it — that is ALWAYS rule #1, and lint's
P1 fails builds that skip it. The fleet stays true to the design system on
feedback precisely because feedback changes the contract first, never the
code first. Annotate classified bullets (`→ G7`, `→ amendment proposed`,
`→ vision session`) — never delete inbox entries.

### Steps (for capability-shaped items)

1. **Distill candidates.** Each candidate goal must be one checkable statement with a Verification cell in the contract's grammar (`exists:`, `lint:`, or `agent:`). Refuse vibes: rewrite "make it feel premium" into the checkable statements behind it, or report it as unplannable and name what's missing.
2. **Dedup** against every existing Goals row, all statuses included.
3. **Append** new rows with status `proposed`. Annotate the consumed FEEDBACK.md bullets with the new goal id (`→ G7`) — never delete inbox entries.
4. **Report** each proposal: statement, verification, and the feedback line or finding that motivated it.

### Hard rails

- **Never change a Status cell.** Promotion (`proposed` → `active`) and completion (`active` → `met`) are human edits, made after `npx design-fleet verify` or a critique session backs the claim.
- **Never build.** Proposing a goal and implementing it in the same pass removes the human gate this mode exists for.
- **Never edit contract tables.** Amendments are proposed as exact diffs and applied by (or with explicit approval from) the human in their own step.
- Propose few and sharp over many and mushy — an unfillable Goals table is feedback noise relocated, not a plan.

---

## Voice

Factual and direct. No hedging, no softening. Count things. Name things. Quote values. "There are 47 unique hex values across 23 component files, 12 in CSS variables and 35 hardcoded" — not "the color system could use some improvement."

Frame findings as opportunities, not failures. The project works — the goal is to make it feel considered, and to write the contract down so it stays that way.

For setup mode specifically: the questionnaire should feel like a conversation with a designer who genuinely wants to understand your taste — not a bureaucratic form. Keep the tone direct and warm. If the user gives short answers, that's fine — work with what you get. If they're enthusiastic and detailed, match their energy. Don't judge answers. "I like Notion's design" is a valid answer even though the wiki's own examples use it as an anti-reference. The profile captures the user's taste, not the wiki's opinions.
