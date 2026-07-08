# Nielsen's 10 Usability Heuristics — Full Reference

Deep reference for design-review's Nielsen Heuristic Mode. Based on [NN/g's 10 Usability Heuristics](https://www.nngroup.com/articles/ten-usability-heuristics/). The heuristics themselves are in the weights — this file pins the severity vocabulary, the sweep protocol, and the concrete patterns this package expects.

Treat the heuristics as a checklist — not every heuristic applies to every change, but scan all 10 before considering an evaluation complete.

---

## Severity Definitions

Rate every violation with exactly one of these four terms. They are the only severity vocabulary in heuristic mode.

| Severity | Definition | Example |
| --- | --- | --- |
| **cosmetic** | Noticeable but does not impede the task. Fix only if time allows. | Icon stroke widths vary slightly between toolbar buttons. |
| **minor** | Causes hesitation or mild irritation; users recover quickly on their own. Low priority. | Truncated cell text with no tooltip — users can still open the record to read it. |
| **major** | Causes significant confusion, inefficiency, or task failure for some users. High priority. | Column headers show raw field names like "instructorId"; users misread the data. |
| **catastrophic** | Blocks task completion or causes data loss. Must fix before release. | Delete with no confirmation and no undo — one misclick destroys a record permanently. |

As a rule of thumb: violations of H3, H5, and H9 trend toward **major/catastrophic** (they block users or lose data); violations of H1, H2, and H4 trend toward **major** (they confuse); violations of H6, H7, H8, and H10 trend toward **minor/cosmetic** (they are polish and optimization). Rate the actual impact, not the heuristic number — a missing loading state on a 10-second operation (H1) can be major, while a missing keyboard shortcut (H7) is almost always minor.

---

## Quick Checklist

When building or reviewing UI, verify:

- [ ] **H1 System status** — Does the UI show what's happening? (loading, saving, success, error)
- [ ] **H2 Real-world match** — Does it use the user's language, not dev jargon? Are icons/metaphors intuitive?
- [ ] **H3 User control** — Can users undo, cancel, go back, or escape from any state?
- [ ] **H4 Consistency** — Does it follow existing patterns in the app and platform conventions?
- [ ] **H5 Error prevention** — Are destructive actions guarded? Are constraints in place before errors happen?
- [ ] **H6 Recognition over recall** — Are options visible? Can users see rather than remember what to do?
- [ ] **H7 Flexibility** — Are there shortcuts for experts? Does it scale for power users?
- [ ] **H8 Minimalist design** — Is every element earning its place? Is the signal-to-noise ratio high?
- [ ] **H9 Error recovery** — Are error messages plain language, specific, and actionable?
- [ ] **H10 Help** — Is contextual help available where users might need it?

---

## Per-Heuristic Patterns

What each heuristic means is in the weights. These are the patterns to check for and the implementations this package expects.

**H1 System status** — Loading states (spinners, skeletons, progress bars), success confirmation (toasts, inline messages), error indicators, state indicators (active tab, selected item, sort direction), real-time feedback (character counts, validate-as-you-type). Implement: toasts for transient messages, inline validation on fields (not just on submit), skeleton loaders, disabled buttons with visual feedback during submission, progress indicators for multi-step flows.

**H2 Real-world match** — Domain language, not developer terms ("Instructor", never "instructorId"); icons match real-world metaphors; locale-appropriate dates, currencies, numbers; field order follows the real-world workflow; familiar metaphors (drag to reorder, swipe to delete).

**H3 User control** — Every modal/dialog has a close button and supports Escape; multi-step flows have back/breadcrumbs; forms have cancel buttons; browser back always works. Implement: undo for deletes (soft delete + undo toast), confirmation dialogs for irreversible actions, Escape closes modals/popovers/dropdowns.

**H4 Consistency** — Same action, same word everywhere (never mix "Delete"/"Remove"/"Trash"); primary action same color and position (right in button groups); list pages and form pages each share a layout. Implement: design tokens for color/spacing/typography, shared components (DataTable, PageHeader, forms), same icon always means same action, consistent empty/loading/error states.

**H5 Error prevention** — Required fields marked and validated before submission; destructive actions confirmed (delete, publish, bulk); sensible defaults; constraints prevent invalid input; destructive actions visually distinct (red). Implement: client-side validation with Zod schemas, input constraints (`type="number"` min/max, maxLength, date pickers), disabled submit until valid, warn before navigating away from unsaved changes.

**H6 Recognition over recall** — Options visible in menus, not shortcut-only; persistent labels on all form fields (never placeholder-only); recently used items or suggestions shown; related info co-located. Implement: combobox/search for fields with many options, breadcrumbs or page titles for location, tooltips on icon-only buttons, inline help on non-obvious fields.

**H7 Flexibility** — Keyboard shortcuts for common actions (Cmd+S, Escape); bulk operations (select all, bulk delete/publish); search and filter on large lists; sortable columns with indicators. Implement: grid/list view toggle, search with debounce on large data sets, keyboard navigation in dropdowns, pagination with configurable page sizes.

**H8 Minimalist design** — Every element earns its place; density fits the context; hierarchy guides the eye; secondary actions visually subordinate. Implement: progressive disclosure, ghost/outline variants for secondary actions vs solid primary, collapsible sections for advanced options, clean empty states, muted colors for secondary information.

**H9 Error recovery** — Plain language (no codes, stack traces, jargon); specific ("Email is invalid", not "Validation error"); actionable ("Enter a valid email address like name@example.com"); errors next to the relevant field, not just at the top. Implement: inline field-level errors below each invalid field, toasts with retry for operation failures, specific Zod error messages, error boundaries with fallback UI, graceful degradation when external services fail.

**H10 Help** — Empty states with guidance and a call-to-action ("No sessions yet. Create your first session."); help text or tooltips on complex fields; contextual help where it's needed, not buried in docs. Implement: helper text below non-obvious inputs, empty-state components with description + CTA button, tooltips on icon-only buttons, page-header descriptions, search placeholders suggesting usage.

---

## Common Violations in CMS/Admin UIs

| Pattern | Heuristic violated | Fix |
| --- | --- | --- |
| No feedback after save/delete | H1 | Add toast notification or inline confirmation |
| Form submits with no validation | H5 | Add client-side validation before submit |
| No way to cancel mid-flow | H3 | Add cancel button, support Escape key |
| Inconsistent button placement | H4 | Standardize primary action position (right) |
| Raw error codes shown to user | H9 | Translate to plain language with recovery steps |
| Empty states with no guidance | H10 | Add helpful empty state with call-to-action |
| Truncated text with no way to see full content | H6 | Add tooltip or expandable row |
| Delete without confirmation | H5 | Add confirmation dialog for destructive actions |
| No loading indicator on async operations | H1 | Add spinner, skeleton, or progress bar |
| Jargon in labels (e.g. "instructorId") | H2 | Use human-readable labels ("Instructor") |

---

## Applying the Heuristics During Build

### When building new UI

1. Before implementation, scan the checklist to anticipate issues
2. Prioritize H1 (status), H3 (control), H5 (error prevention) — these prevent the most user frustration
3. After implementation, do a final pass against all 10

### When reviewing existing UI

1. Walk through each user flow as if seeing it for the first time
2. Check all 10 heuristics in order — never just the obvious violations
3. Rate every violation cosmetic / minor / major / catastrophic and pair it with a concrete fix
