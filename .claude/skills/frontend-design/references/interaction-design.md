# Interaction Design Reference

Deep implementation guide for interactive states, focus management, modals, popovers, dropdowns, and keyboard navigation.

## The 8 Interactive States

Every interactive element must have these states designed:

| State | When | Visual Treatment |
|-------|------|------------------|
| Default | At rest | Base styling |
| Hover | Pointer over (not touch) | Subtle lift, color shift |
| Focus | Keyboard/programmatic | Visible ring (see below) |
| Active | Being pressed | Pressed in, darker |
| Disabled | Not interactive | Reduced opacity, no pointer events |
| Loading | Processing | Spinner replacing label, or skeleton |
| Error | Invalid state | Red border, icon, message |
| Success | Completed | Green check, confirmation |

Designing hover without focus — or focus without active — is incomplete. Keyboard users never see hover states.

## Focus Rings

```css
/* Hide for mouse, show for keyboard */
button:focus {
  outline: none;
}

button:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}
```

Focus ring requirements:
- 3:1 contrast minimum against adjacent colors
- 2-3px thick
- Offset from the element (not inside it)
- Consistent across all interactive elements

## Native Dialog

```html
<dialog id="confirm-dialog">
  <h2>Delete project?</h2>
  <p>This can't be undone.</p>
  <form method="dialog">
    <button value="cancel">Keep project</button>
    <button value="confirm" autofocus>Delete project</button>
  </form>
</dialog>
```

```js
const dialog = document.querySelector('#confirm-dialog');
dialog.showModal(); // Focus trap, Escape closes, backdrop
```

`showModal()` gives you focus trapping, Escape to close, and `::backdrop` styling — no JavaScript required for these behaviors.

### The inert Attribute

```html
<main inert>
  <!-- Behind modal — can't be focused, clicked, or read by screen readers -->
</main>
<dialog open>
  <!-- Focus stays here -->
</dialog>
```

## Popover API

For tooltips, dropdowns, and non-modal overlays:

```html
<button popovertarget="menu">Open menu</button>
<div id="menu" popover>
  <button>Option 1</button>
  <button>Option 2</button>
</div>
```

Benefits: light-dismiss (click outside closes), proper stacking (top layer), no z-index wars, accessible by default.

## Dropdown Positioning

The most common dropdown bug: `position: absolute` inside `overflow: hidden` clips the dropdown.

### CSS Anchor Positioning (Chrome 125+)

```css
.trigger {
  anchor-name: --menu-trigger;
}

.dropdown {
  position: fixed;
  position-anchor: --menu-trigger;
  position-area: block-end span-inline-end;
  margin-top: 4px;
}

@position-try --flip-above {
  position-area: block-start span-inline-end;
  margin-bottom: 4px;
}
```

### Portal Pattern (React)

```tsx
import { createPortal } from 'react-dom';

function Dropdown({ trigger, children }) {
  const [coords, setCoords] = useState({ top: 0, left: 0 });
  const triggerRef = useRef(null);

  const open = () => {
    const rect = triggerRef.current.getBoundingClientRect();
    setCoords({ top: rect.bottom + 4, left: rect.left });
  };

  return (
    <>
      <button ref={triggerRef} onClick={open}>{trigger}</button>
      {isOpen && createPortal(
        <div style={{ position: 'fixed', ...coords }}>
          {children}
        </div>,
        document.body
      )}
    </>
  );
}
```

### Z-Index Scale

Use semantic values, not arbitrary numbers:

| Layer | Z-Index |
|-------|---------|
| Dropdown | 100 |
| Sticky header | 200 |
| Modal backdrop | 300 |
| Modal | 400 |
| Toast | 500 |
| Tooltip | 600 |

## Roving Tabindex

For component groups (tabs, menus, radio groups), one item is tabbable. Arrow keys move within the group:

```html
<div role="tablist">
  <button role="tab" tabindex="0" aria-selected="true">Tab 1</button>
  <button role="tab" tabindex="-1">Tab 2</button>
  <button role="tab" tabindex="-1">Tab 3</button>
</div>
```

```js
tablist.addEventListener('keydown', (e) => {
  const tabs = [...tablist.querySelectorAll('[role="tab"]')];
  const current = tabs.indexOf(document.activeElement);

  if (e.key === 'ArrowRight') {
    const next = (current + 1) % tabs.length;
    tabs[current].tabIndex = -1;
    tabs[next].tabIndex = 0;
    tabs[next].focus();
  }
  if (e.key === 'ArrowLeft') {
    const prev = (current - 1 + tabs.length) % tabs.length;
    tabs[current].tabIndex = -1;
    tabs[prev].tabIndex = 0;
    tabs[prev].focus();
  }
});
```

Tab moves to the next component entirely. Arrow keys navigate within.

## Skip Links

```html
<a href="#main" class="skip-link">Skip to main content</a>
<nav><!-- ... --></nav>
<main id="main"><!-- ... --></main>
```

```css
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  z-index: 9999;
}

.skip-link:focus {
  top: 0;
  padding: 8px 16px;
  background: var(--color-primary);
  color: white;
}
```

Hidden by default, visible on keyboard focus. Lets keyboard users jump past navigation.

## Form Design

- Always use visible `<label>` elements — placeholders disappear on input
- Validate on blur, not on every keystroke (exception: password strength)
- Place errors below fields with `aria-describedby` connecting them
- Use `inputmode` for mobile keyboards: `inputmode="numeric"` for phone numbers, `inputmode="email"` for email

```html
<label for="phone">Phone number</label>
<input
  id="phone"
  type="tel"
  inputmode="numeric"
  pattern="[0-9]*"
  aria-describedby="phone-help"
/>
<p id="phone-help">Include country code (e.g., +1 555 123 4567)</p>
```

## Gesture Discoverability

Swipe-to-delete and similar gestures are invisible. Always:
- Partially reveal the action (show delete button peeking from edge)
- Provide a visible fallback (context menu with "Delete")
- Coach on first use (onboarding hint)

Never rely on gestures as the only way to perform an action.
