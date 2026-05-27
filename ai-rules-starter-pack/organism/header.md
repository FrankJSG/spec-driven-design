# Header — AI Generation Rules

> Copy section 7 below into your Header L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Header L2 spec may produce:

- Top navigation header with declared zones: logo/wordmark (left), primary navigation (center or left), utility actions (right)
- Responsive behavior: full nav on desktop, hamburger menu trigger on mobile (if declared in scope)
- Mobile navigation panel: slide-in or dropdown from header, with all nav items, utility actions, and a close mechanism
- Active state indicator for the current route (underline, color change, or background — per spec variant)
- Notification badge on utility icons (using the system's Badge component L2 spec)
- User avatar / account menu trigger with dropdown (if in scope, references Dropdown L2)
- Sticky / fixed header behavior with scroll-aware shadow elevation transition
- Skip-to-main-content link: hidden until focused, appears at top of header for keyboard users
- ARIA: `role="banner"` on the `<header>` element, `role="navigation"` on the nav element with `aria-label="Main navigation"`, `aria-current="page"` on the active nav item
- Mobile menu toggle: `aria-expanded` state on the toggle button, `aria-controls` pointing to the menu panel ID

### Validate

Before approving any generated Header output, verify:

- [ ] `<header>` element is used (not `<div role="header">`)
- [ ] `role="banner"` is implicit on `<header>` — do not add redundantly; verify it is not overridden
- [ ] `<nav>` element with `aria-label="Main navigation"` wraps navigation links
- [ ] Active nav item has `aria-current="page"` attribute
- [ ] Skip-to-main-content link is the first focusable element in the DOM
- [ ] Mobile menu toggle uses `aria-expanded` (true/false) and `aria-controls`
- [ ] Mobile menu is hidden from screen readers when closed (`aria-hidden="true"` or `display: none`)
- [ ] Header height uses a spacing token or a fixed height token declared in the spec
- [ ] Background uses `color-surface-raised` or `color-surface-base` per the declared header variant
- [ ] Scroll shadow transition uses `shadow-sm` → `shadow-md` tokens and `motion-duration-fast` easing
- [ ] All icon-only utility buttons have `aria-label` with a descriptive name

### Forbidden

An AI agent working on Header must never:

- Generate navigation links as `<div>` elements — all clickable nav items must be `<a>` or `<button>`
- Omit the skip-to-main-content link (this is a WCAG 2.4.1 requirement, not optional)
- Generate two `<header>` elements on the same page (one landmark banner per page)
- Create a mobile menu that is `display: none` with transition (this blocks screen readers unpredictably — use `visibility: hidden` + `aria-hidden` for animated close)
- Generate dropdown menus that open on hover only (keyboard users cannot trigger hover; use click/Enter with keyboard support)
- Use `position: sticky` without also handling z-index correctly (stacking context issues are a common generation error)
- Add notification counts directly in text (e.g., "Notifications (3)") — use the Badge component
- Generate a header with more than one primary CTA in the utility zone (two competing primary buttons = emphasis failure)

### Prompt Seed

```
Generate a Header (top navigation) component for [System Name].

Reference: SDDn Behavior Spec [HDR-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Layout zones:
- Left: logo/wordmark (link to home)
- Center/Left: primary navigation links ([list nav items])
- Right: [utility actions — e.g., notifications, user menu, CTA button]

Responsive behavior: full nav above [breakpoint], hamburger menu below
Position: sticky (scroll-aware shadow)
Framework: [React / Vue / HTML — specify]

Token constraints:
- Background: color-surface-raised
- Height: [spacing token or value per spec]
- Logo area width: [value]
- Nav link text: typography-label-md, color-text-primary
- Nav link active: color-action-primary, aria-current="page"
- Utility icon size: [specify]
- Shadow on scroll: shadow-sm → shadow-md, motion-duration-fast
- Mobile menu background: color-surface-raised
- Mobile menu overlay: color-surface-overlay

Component references:
- Notification badge: [BADGE-L2-NNN] v[version]
- CTA button: [BTN-L2-NNN] v[version]

Accessibility:
- <header> element, <nav aria-label="Main navigation">
- Skip-to-main-content: first focusable element
- aria-current="page" on active nav item
- Mobile toggle: aria-expanded, aria-controls
- All icon buttons: aria-label

Do not generate: hover-only dropdown triggers, div-as-nav-link, pages without skip link.
```
