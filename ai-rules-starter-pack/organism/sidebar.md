# Sidebar — AI Generation Rules

> Copy section 7 below into your Sidebar L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Sidebar L2 spec may produce:

- Sidebar navigation panel with declared width, background, and zone structure: top (logo/product name), middle (nav groups with items), bottom (settings, user, logout)
- Nav item states: default, hover, active (current route), focused, disabled
- Collapsible sidebar: icon-only collapsed state, full-width expanded state; toggle button with `aria-expanded`
- Collapsible nav groups (accordion): group label as trigger, items as collapsible children; individual group open/close with keyboard support
- Tooltip on collapsed icon items (shows full label on hover/focus when sidebar is collapsed)
- Nested navigation: max 2 levels deep; level 2 indented using spacing tokens, visually distinct but not a new component
- Badge/count on nav items (references system Badge L2 spec)
- Responsive behavior: persistent sidebar on desktop, slide-in drawer on mobile (if in scope)
- ARIA: `<nav>` with `aria-label="Application navigation"`, active item with `aria-current="page"`, collapsible groups use `aria-expanded` on the trigger

### Validate

Before approving any generated Sidebar output, verify:

- [ ] `<nav>` element with `aria-label="Application navigation"` wraps the entire sidebar navigation
- [ ] Active nav item has `aria-current="page"` attribute
- [ ] Sidebar width uses a declared token or value from the spec — not an arbitrary pixel value
- [ ] Background uses a declared token (typically `color-surface-raised` or a distinct sidebar surface token)
- [ ] Nav item active state uses `color-action-primary` for text/icon and a distinct background token — both color and background change (not color alone)
- [ ] Nav item focus state shows focus ring inside the sidebar surface — not just a browser default
- [ ] Collapsed sidebar: icon-only items have `aria-label` with full item name (or tooltip with `role="tooltip"` and `aria-describedby`)
- [ ] Collapsible group trigger uses `aria-expanded` (true/false)
- [ ] Mobile drawer has focus trap when open, returns focus on close
- [ ] Nav item height meets 44px minimum touch target
- [ ] Nesting does not exceed 2 levels — deeper navigation belongs in a different pattern

### Forbidden

An AI agent working on Sidebar must never:

- Generate sidebar navigation items as non-link, non-button elements (all nav items must be `<a>` for routes or `<button>` for actions)
- Create a third level of nested navigation — 3-level trees in a sidebar create navigability and usability problems
- Generate the collapsed sidebar without tooltips on icon items (icon-only navigation without labels is an accessibility failure)
- Use `display: none` on the sidebar for the mobile-closed state without also setting `aria-hidden="true"` and ensuring focus cannot enter it
- Add scroll behavior inside the sidebar without ensuring the scroll container is keyboard-scrollable (`tabindex="0"` on the scroll container if needed)
- Generate bottom-zone items (settings, logout) with the same visual weight as primary nav items — they must be visually separated using a border or spacing token
- Mix active state styles across nav levels (the active indicator must be consistent across levels 1 and 2)

### Prompt Seed

```
Generate a Sidebar navigation component for [System Name].

Reference: SDDn Behavior Spec [SBR-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Nav items: [list top-level nav groups and items]
Zones: top (logo), main nav (scrollable), bottom (settings, user profile, logout)
Collapsible: yes — icon-only collapsed state, full-width expanded
Responsive: persistent above [breakpoint], drawer below
Framework: [React / Vue / HTML — specify]

Token constraints:
- Background: color-surface-raised (or sidebar-specific token if declared)
- Width (expanded): [value from spec]
- Width (collapsed): [value from spec]
- Nav item height: 44px minimum (spacing token alignment)
- Nav item padding: spacing-2 (vertical) × spacing-3 (horizontal)
- Nav item active background: [token]
- Nav item active text: color-action-primary
- Nav item hover background: [token]
- Section gap (between nav groups): spacing-4
- Bottom zone separator: color-border-default
- Transition (expand/collapse): motion-duration-standard + motion-ease-standard

Component references:
- Nav item badges: [BADGE-L2-NNN] v[version]

Accessibility:
- <nav aria-label="Application navigation">
- aria-current="page" on active item
- Collapsed icons: aria-label or tooltip with role="tooltip"
- Collapsible groups: aria-expanded on trigger
- Mobile drawer: focus trap + return focus on close

Do not generate: 3-level nested nav, icon-only items without labels, non-link/button nav items.
```
