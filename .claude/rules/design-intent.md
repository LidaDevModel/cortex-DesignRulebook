
# Design Fleet -- Design Intent

> **Project contract precedence:** if the project has a `VISION.md` hard-rails contract, its tokens, timings, wording tables, and patterns override the defaults in these rules. The rules apply where the contract is silent.

## Vision Contract Check

If `VISION.md` exists at the project root and the change targets an **existing surface**, skip the generative questions below and run the contract's own pre-edit checklist instead — conformance beats novelty once a signature exists. Use the questions below for **new surfaces** the contract doesn't cover, and propose adding what you decide to `VISION.md` (via the `design-contract` skill) so the next edit inherits it.

## Taste Profile Check

Before proceeding with any UI work, check if a taste profile exists: `.cursor/rules/design-taste.mdc` or `.claude/rules/design-taste.md` at the project level, or a personal one under `~/.cursor` or `~/.claude`. If no taste profile exists, invoke the `design-contract` skill to establish one before continuing. The taste profile ensures every design decision reflects the user's aesthetic — not generic defaults. Exception: when `VISION.md` covers the surface being edited, a missing taste profile does not block the work — offer `design-contract` instead of requiring it.

If a taste profile exists, read it and use it as context for answering the questions below. The profile's aesthetic direction, references, and preferences are the starting defaults — the questions below refine them for each specific screen.

When selecting a skill, consult `skill-manifest.json` in `.cursor/` or `.claude/` for machine-readable routing (triggers, inputs, outputs, and skill boundaries).

## Reuse Gate

Before structuring, laying out, or composing anything, find how this codebase already solves the same problem. **Reuse beats invention** — and re-deriving a pattern from scratch, even if you land somewhere similar, is a failure: the detour produces a subtly different answer that fragments the system.

- **Search by the problem, not the name.** "How does this app already gate a query / share state across views / lay out a list / build a modal?" Check the contract, the component inventory (`COMPONENTS.md` when present), shared primitives, and the nearest analogous feature. If you haven't opened the existing implementation, you're not ready to answer.
- **Lead with what exists.** Phrase it as "we already do this in `<file:line>` — here's how it adapts," not as a fresh concept. Match the house conventions (naming, layering, state, composition) even where your taste differs — consistency the user never notices beats cleverness they feel as friction.
- **Earn the word "new."** Only call something net-new after checking the contract, inventory, and shared primitives and finding nothing — then say so: "I searched X, Y, Z; none cover this."
- **Recency is a trap.** The files you just touched are not the architecture. Map the request onto the whole system, not whatever's freshest in context.
- **Reuse OR propose evolving — never silently diverge.** If the existing pattern is genuinely deficient, the move is to *improve the pattern in place* (and write the decision back into `VISION.md` via `design-contract`), so every consumer benefits and the next agent inherits it. Quietly forking a "better" variant is the fragmentation this gate exists to prevent.

**Gate:** if you cannot point to the pattern you're reusing — or state specifically that you searched and found none — you have skipped this. Stop and search first.

## Scope Check

If the user's request is scoped to a single component edit, bug fix, style tweak, or small addition — and a taste profile already exists — skip the questionnaire below and apply the existing taste profile and active rules directly. Only run the full questionnaire for new pages, new features, full-screen designs, or when no taste profile exists and no VISION.md covers the surface.

## The Questions

Before generating UI code, answer these questions. Skip none.

1. **What one word describes how this should feel?** — Calm, fast, warm, precise, playful, confident, minimal, dense, editorial, premium. Pick one. Every decision flows from it.

2. **What's the user's state of mind?** — Stressed (checkout, error, deadline)? Focused (editor, form, dashboard)? Browsing (discovery, feed, settings)? The UI must respect that state — not fight it.

3. **What's the single most important element on this screen?** — Name it. If you can't, the hierarchy is wrong. If everything is important, nothing is.

4. **What's the reference?** — Name a real product that nails what this should feel like. "This sidebar should feel like Linear's." "This card should feel like Airbnb's listing." A concrete reference beats abstract adjectives.

5. **What can be removed?** — Before adding anything, audit what's already there. Every element must earn its place. If removing it doesn't hurt the user's ability to complete their task — remove it.

6. **What's the unexpected detail?** — After the UI works and looks clean, what's the one small thing that would make someone pause? A considered empty state. A satisfying confirmation moment. A hover effect that rewards curiosity. Build the expected first, then add the memorable.

7. **Does it pass the squint test?** — Blur your eyes (or blur a screenshot). Can you still identify the most important element? The second? Clear groupings? If everything looks the same weight blurred, the hierarchy is broken.

8. **Would someone say "AI made this"?** — If you showed this interface to a designer and said "AI generated this," would they believe you immediately? If yes, that's the problem. A distinctive interface makes someone ask "how was this made?" — not "which AI made this?" Check for: default fonts, purple gradients, card-in-card nesting, identical card grids, gray text on colored backgrounds, bounce animations.

## Principles

- **Intent before implementation** — Decide what it should feel like before deciding how to build it. The feeling determines the font, the spacing, the animation, the density — not the other way around.
- **Reduction is taste** — The best interfaces have less than you'd expect. Generous whitespace where users breathe, density where they need efficiency. Default to less — unless the design intent (Question 1) calls for maximalism. The point is intentionality, not minimalism.
- **Emotion is functional** — A calm checkout reduces abandonment. A fast dashboard builds trust. A warm onboarding increases completion. Emotion isn't decoration — it's the product working.
- **Consistency is invisible, inconsistency is loud** — Users never notice consistent spacing, matching shadows, aligned type. They immediately feel when something is off. Invisible is the goal.
- **Ship the third version** — First pass: it works. Second pass: it looks right. Third pass: it feels right. Don't stop at the first.
