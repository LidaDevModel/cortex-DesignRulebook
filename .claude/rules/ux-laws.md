---
paths: "**/*.{tsx,jsx,ts,js,css,scss,html,vue,svelte}"
---

# Design Fleet -- UX Laws

> **Project contract precedence:** if the project has a `VISION.md` hard-rails contract, its stricter values win (e.g. a contract mandating 44px hit targets beats the 32px floor below). These laws apply where the contract is silent.

23 laws, one directive each. The reasoning is in the weights; the slugs and numbers are the contract.

- `ux-fitts-target-size` -- Interactive elements must be at least 32px (44px when the project contract or platform guidelines demand it — the stricter floor wins).
- `ux-fitts-hit-area` -- Expand hit areas beyond visible bounds (pseudo-elements or invisible padding).
- `ux-hicks-minimize-choices` -- Minimize visible choices; use progressive disclosure.
- `ux-millers-chunking` -- Chunk data into groups of 5-9 items.
- `ux-doherty-under-400ms` -- Interactions respond within 400ms.
- `ux-doherty-perceived-speed` -- Can't be fast? Fake it: skeletons, optimistic UI, progress indicators.
- `ux-postels-accept-messy-input` -- Accept messy human input, output clean normalized data.
- `ux-progressive-disclosure` -- Show what matters now; reveal complexity incrementally.
- `ux-teslers-complexity` -- Irreducible complexity goes to the system, not the user.
- `ux-cognitive-load-reduce` -- Remove anything that doesn't help the user complete the task.
- `ux-jakobs-familiar-patterns` -- Familiar patterns by default; deviations must be intentional improvements.
- `ux-proximity-grouping` -- Tighter spacing within groups, larger spacing between them.
- `ux-similarity-consistency` -- Same function, same look.
- `ux-common-region-boundaries` -- Group related content with boundaries: cards, sections, dividers.
- `ux-uniform-connectedness` -- Connect related elements with lines, color, or frames.
- `ux-pragnanz-simplify` -- Simplify complex visuals to their clearest form.
- `ux-von-restorff-emphasis` -- Make the most important element visually distinct.
- `ux-serial-position` -- Key items go first or last in sequences.
- `ux-aesthetic-usability` -- Polish compounds into perceived usability and trust.
- `ux-peak-end-finish-strong` -- End flows with clear, satisfying success states.
- `ux-goal-gradient-progress` -- Show progress toward completion.
- `ux-zeigarnik-show-incomplete` -- Show incomplete state to drive completion.
- `ux-pareto-prioritize-features` -- Optimize the critical-path 20% of features first.
