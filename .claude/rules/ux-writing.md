
# Design Fleet -- UX Writing

> **Project contract precedence:** if the project has a `VISION.md` hard-rails contract, its exact phrasing tables and wording rules override the defaults below. These rules apply where the contract is silent.

Every word in the interface is a design decision. Make each one earn its place.

## Button Labels

- `writing-specific-verbs` -- Use verb + object: "Save changes", "Create account", "Download PDF". Never "OK", "Submit", "Yes/No", or "Click here".
- `writing-name-the-destruction` -- Destructive buttons name the action: "Delete project" not "Remove". Show count: "Delete 5 items" not "Delete selected".
- `writing-cancel-means-what` -- "Cancel" is ambiguous. Say what happens: "Keep editing", "Discard changes", "Go back".

## Error Messages

- `writing-error-formula` -- Every error answers three questions: What happened? Why? How to fix it? "Email isn't valid. Please include an @ symbol." not "Invalid input."
- `writing-dont-blame-the-user` -- "Please enter a date in MM/DD/YYYY format" not "You entered an invalid date." Reframe errors as guidance.
- `writing-specific-over-generic` -- "We couldn't save your changes — check your connection and try again" not "Something went wrong."

## Empty States

- `writing-empty-states-teach` -- Empty states are onboarding moments. Acknowledge briefly, explain the value, provide a clear action. "No projects yet. Create your first one to get started." — not just "No items."

## Terminology

- `writing-one-term-one-meaning` -- Pick one word per concept and use it everywhere. Delete, not Delete/Remove/Trash. Settings, not Settings/Preferences/Options. Sign in, not Sign in/Log in/Enter. Build a glossary and enforce it.

## Redundancy

- `writing-say-it-once` -- If the heading explains it, the subheading is redundant. If the button is clear, don't explain it again. Never repeat information the user can already see.

## Loading & Confirmation

- `writing-specific-loading` -- "Saving your draft..." not "Loading..." For long waits, set expectations: "This usually takes 30 seconds."
- `writing-undo-over-confirm` -- Undo is better than confirmation dialogs — users click through confirmations mindlessly. Remove from UI immediately, show undo toast, actually delete after toast expires. Use confirmation only for truly irreversible or high-cost actions.

## Tone Adaptation

- `writing-tone-matches-moment` -- Voice (brand personality) is consistent everywhere. Tone adapts: celebratory for success ("Done — your changes are live."), empathetic for errors ("That didn't work. Here's what to try..."), reassuring for loading ("Saving your work..."), serious for destruction ("Delete this project? This can't be undone."). Celebration never needs an exclamation mark.
- `writing-no-humor-for-errors` -- Never use humor in error messages. Users are already frustrated. Be helpful, not cute.

## Accessibility

- `writing-meaningful-link-text` -- "View pricing plans" not "Click here". Link text must make sense out of context for screen readers.
- `writing-alt-text-describes-information` -- Alt text describes information, not the image: "Revenue increased 40% in Q4" not "Chart". Use `alt=""` for decorative images.

## Translation Readiness

- `writing-plan-for-expansion` -- German text is ~30% longer than English. Design layouts that tolerate text expansion without breaking.
- `writing-numbers-separate` -- Keep numbers as separate parameters: "New messages: {count}" not "You have {count} new messages" — word order varies by language.
