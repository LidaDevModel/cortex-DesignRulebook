# Design Fleet

This project runs a fleet of design agents: three role-aligned skills —
design-contract (owns the taste profile and the VISION.md contract),
frontend-design (builds distinctive UI and owns motion), and design-review
(deterministic lint floor + subjective critique ceiling) — plus rules for
standing hygiene and a deterministic lint that enforces what models apply
unreliably.

## How it works
- Design rules live in `.claude/rules/*.md`. The two always-on rules (design-intent, ux-writing) have no `paths:` field, so they load at launch; the rest declare `paths:` and load when you read matching files (UI, CSS, etc.).
- Design skills live in `.claude/skills/`. Describe your task and the right skill will be picked, or invoke directly with `/skill-name`.
- Skill routing logic is documented in `.claude/skill-manifest.json`.

## Recommended workflow
1. New UI work without a taste profile? Run `/design-contract` (setup mode) first.
2. Existing project? Use `/design-contract` — assess the codebase, or draft the VISION.md project contract.
3. Building components/pages? Use `/frontend-design` (it honors VISION.md when present).
4. After generating UI, run `/design-review` to verify rules — and the contract — were followed.

If `VISION.md` exists at the project root, it is the binding design contract:
it overrides the taste profile and these rules wherever it speaks.
