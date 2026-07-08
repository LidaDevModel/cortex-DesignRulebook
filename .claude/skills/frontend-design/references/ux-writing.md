# UX Writing Reference

Deep implementation guide for interface copy — button labels, error messages, empty states, and translation-ready patterns. For principles and rules, see the `ux-writing.mdc` rule. This file covers code templates, implementation patterns, and translation-readiness details.

## Button Label Patterns

Concrete replacements (see `ux-writing.mdc` for the underlying rules):

| Bad | Good | Why |
|-----|------|-----|
| OK | Save changes | Says what will happen |
| Submit | Create account | Outcome-focused |
| Yes | Delete message | Confirms the action |
| Cancel | Keep editing | Clarifies what "cancel" means |
| Click here | Download PDF | Describes the destination |
| Delete selected | Delete 5 items | Shows the count |
| Remove | Delete | Delete is permanent, remove implies recoverable |

## Error Message Templates

Reusable templates for common scenarios (see `writing-error-formula` in `ux-writing.mdc` for the three-question framework):

| Situation | Template |
|-----------|----------|
| Format error | "[Field] needs to be [format]. Example: [example]" |
| Missing required | "Please enter [what's missing]" |
| Permission denied | "You don't have access to [thing]. [What to do instead]" |
| Network error | "We couldn't reach [thing]. Check your connection and [action]." |
| Server error | "Something went wrong on our end. We're looking into it. [Alternative action]" |

### Implementation

```tsx
// BAD
<p className="error">Invalid input</p>

// GOOD
<p className="error" role="alert">
  Email address isn't valid. Please include an @ symbol.
</p>
```

Always use `role="alert"` or `aria-live="polite"` so screen readers announce the error.

Connect errors to fields:

```tsx
<label htmlFor="email">Email</label>
<input id="email" aria-describedby="email-error" />
<p id="email-error" className="error">
  Please enter a valid email address.
</p>
```

## Empty State Patterns

```tsx
// BAD
<div className="empty">No items</div>

// GOOD
<div className="empty-state">
  <h3>No projects yet</h3>
  <p>Create your first project to start tracking progress.</p>
  <Button>Create project</Button>
</div>
```

The three elements: acknowledge (what's empty), explain (why it matters), action (what to do).

## Undo vs. Confirm Implementation

```tsx
// Confirmation dialog (weaker — users click through mindlessly)
const handleDelete = () => {
  if (confirm("Delete this item?")) deleteItem(id);
};

// Undo pattern (stronger — immediate feedback, recoverable)
const handleDelete = () => {
  hideItem(id);
  toast({
    message: "Item deleted",
    action: { label: "Undo", onClick: () => restoreItem(id) },
    duration: 5000,
    onClose: () => permanentlyDelete(id),
  });
};
```

Use confirmation only for: account deletion, batch operations affecting many items, actions with external consequences (sending emails, publishing).

## Terminology Glossary Template

Maintain a glossary in the project. Enforce it in code review.

| Concept | Use | Don't Use |
|---------|-----|-----------|
| Remove permanently | Delete | Remove, Trash, Erase |
| User preferences | Settings | Preferences, Options, Config |
| Authentication | Sign in / Sign out | Log in, Enter, Login |
| Make new | Create | Add, New, Make |
| Modify | Edit | Update, Change, Modify |

## Translation-Ready Patterns

```tsx
// BAD — concatenation breaks in other languages
`You have ${count} new messages`

// GOOD — full sentence as one string with interpolation
t('messages.new', { count })
// en: "New messages: {{count}}"
// de: "Neue Nachrichten: {{count}}"

// BAD — abbreviation
"5 mins ago"

// GOOD — full words
"5 minutes ago"
```

| Language | Expansion vs English |
|----------|---------------------|
| German | +30% |
| French | +20% |
| Finnish | +30-40% |
| Chinese | -30% (fewer chars, but similar width) |

Design layouts that tolerate 30-40% text expansion without breaking. Test with pseudo-localization (replace text with elongated versions) early.

## Accessible Writing Checklist

- [ ] All link text is meaningful out of context ("View pricing" not "Click here")
- [ ] All images have descriptive alt text or `alt=""` for decorative
- [ ] All icon buttons have `aria-label`
- [ ] Error messages are connected with `aria-describedby`
- [ ] Loading states are announced with `aria-live="polite"`
- [ ] Destructive buttons clearly name the action and scope
