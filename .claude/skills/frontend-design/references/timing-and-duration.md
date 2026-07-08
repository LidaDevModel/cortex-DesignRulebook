# Timing and Duration

How long animations should last, and the frequency rule that governs
when to skip animation entirely.

---

## Duration Table

| Element type | Duration |
| --- | --- |
| Micro-interactions (press, hover) | 120-180ms |
| Standard UI (tooltips, dropdowns) | 150-250ms |
| Small state changes | 180-260ms |
| Modals, drawers | 200-300ms |

**Rules:**

- UI animations must stay under 300ms
- Larger elements animate slower than smaller ones
- Exit animations can be ~20% faster than entrance
- Match duration to distance — longer travel = longer duration
- If animation feels slow, **shorten the duration first** before
  adjusting the curve

## The Frequency Rule

Determine how often users will see the animation:

- **100+ times/day** → no animation (or drastically reduced)
- **Occasional use** → standard animation
- **Rare/first-time** → can be more special

This single rule eliminates more bad motion than any other guideline.
A button users click 200 times a day should feel instant; a "welcome"
sequence they see once can take its time.
