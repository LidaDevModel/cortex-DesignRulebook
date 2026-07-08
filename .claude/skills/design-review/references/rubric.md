# Design-review scored rubric (subjective ceiling)

The deterministic floor (`npx design-fleet lint`) already enforces the objective,
grep-able rules — off-token colors, banned fonts, off-scale spacing. This rubric
is for the **subjective ceiling**: judging design *quality* on what no regex can
check. It produces a single **0–1 score plus a required justification paragraph**
— never a bare number (LOOPS rule VI: "a number between zero and one and a
paragraph explaining the gap").

> **This score only GATES when calibrated.** Before it can block anything, score
> the calibration corpus (`evals/calibration/manifest.json`) and run the result
> through `lib/calibration.mjs` `checkCalibration()`. If the judge can't separate
> the good anchors from the slop ones by the margin, the score is **advisory
> only** and the lint floor stays the sole gate. See SKILL.md → *Scored mode*.
>
> **Judge the rendered pixels, not the code.** A taste score read off JSX grades
> imagined output. Score an actual screenshot of the rendered screen (the host
> renders it — the package ships no browser).

## Axes (weighted)

| Axis | Weight | What it measures |
|---|---|---|
| **Design** | 0.35 | Visual hierarchy that guides the eye, intentional color, typographic craft, spacing rhythm, elevation/depth handled deliberately. |
| **Craft** | 0.25 | The refinement-pass details: optical alignment, concentric radii, same-type controls treated identically, quiet off/unselected states, considered empty/loading/error states. |
| **Originality** | 0.15 | A point of view. Avoids the generic AI-default aesthetic (Inter, `#fff`/`#000`, purple-on-white gradients, stock component shapes). Distinctive without being noisy. |
| **Functionality / UX** | 0.25 | Does the flow work — affordances are discoverable, states are handled, interaction is consistent, accessibility floors are met (focus, contrast as rendered, touch targets). |

**Overall = weighted sum of the four axis scores.** Weights sum to 1.0.

## The 0–1 scale (per axis)

| Score | Meaning |
|---|---|
| **0.0–0.3** | Generic / broken. Looks like a default template or AI slop; or the axis is actively wrong (no hierarchy, unreadable, dead-ends). |
| **0.4–0.6** | Competent but unremarkable. Nothing wrong, nothing memorable — the "fine" zone most output lands in. |
| **0.7–0.8** | Considered. Clear intent, good craft, a coherent point of view. |
| **0.9–1.0** | Exceptional. The kind of detail that makes someone who cares pause. Reference-quality. |

## Required output per screen

```json
{
  "axes": {
    "design":        { "score": 0.0, "why": "specific, cites what you see in the render" },
    "craft":         { "score": 0.0, "why": "..." },
    "originality":   { "score": 0.0, "why": "..." },
    "functionality": { "score": 0.0, "why": "..." }
  },
  "overall": 0.0,
  "summary": "one paragraph — the gap between this and reference-quality, concretely"
}
```

- Every `why` must reference **something visible in the rendered screenshot** — not the code, not a guess. No bare scores.
- When a `VISION.md` contract exists, the floor has already checked its token/string tables; here, judge whether the screen *feels* on-brand and honors the contract's canonical examples — the part a contract can't make deterministic.
- Be a critic, not an optimist. A score that can't fail you isn't a gate. Default toward the honest lower number and say exactly what's missing.
