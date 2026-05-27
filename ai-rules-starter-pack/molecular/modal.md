# Modal — AI Generation Rules

> Copy section 7 below into your Modal L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Modal L2 spec may produce:

- Modal container with backdrop overlay, dialog panel, and close mechanism
- All declared variants: informational, confirmation (with destructive action), form modal, alert (non-dismissible until action taken)
- Size variants: sm (simple alerts), md (standard), lg (forms, rich content) — using max-width tokens
- Entry/exit animations: fade-in with subtle scale using declared motion tokens; `prefers-reduced-motion` variant with no animation
- Focus management: on open, focus moves to the first focusable element (or heading); on close, focus returns to the trigger element
- Focus trap: Tab cycles through focusable elements inside modal only; Shift+Tab cycles backward; Tab from last element returns to first
- Keyboard close: Escape key closes the modal (except non-dismissible `alert` variant)
- Backdrop click close: clickable backdrop for dismissible variants; non-clickable for `alert` variants
- Footer layout: action buttons right-aligned for confirmation modals; full-width for mobile
- ARIA: `role="dialog"`, `aria-modal="true"`, `aria-labelledby` pointing to modal heading, `aria-describedby` pointing to modal body
- Scroll lock on body when modal is open
- Portal rendering to `<body>` to avoid z-index and overflow clip issues

### Validate

Before approving any generated Modal output, verify:

- [ ] `role="dialog"` and `aria-modal="true"` are present on the dialog container
- [ ] `aria-labelledby` references the modal's heading element ID
- [ ] `aria-describedby` references the modal's body content ID (for informational content)
- [ ] Focus moves into the modal on open — not left on the trigger button
- [ ] Focus trap is active: Tab does not leave the modal while it is open
- [ ] Focus returns to the trigger element when modal closes
- [ ] Escape closes the modal on dismissible variants
- [ ] Non-dismissible alert modal does NOT close on Escape or backdrop click
- [ ] Backdrop uses `color-surface-overlay` token at the system's declared opacity
- [ ] Modal panel uses `color-surface-raised` token and the declared elevation shadow
- [ ] Entry animation uses `motion-duration-standard` and `motion-ease-enter` tokens
- [ ] Exit animation uses `motion-duration-fast` and `motion-ease-exit` tokens
- [ ] `@media (prefers-reduced-motion: reduce)` disables entry/exit animation
- [ ] Scroll lock on body is applied on open and removed on close

### Forbidden

An AI agent working on Modal must never:

- Generate a modal without a focus trap — an unfocused modal is an accessibility failure
- Use `z-index` values not defined in the system's token contract (hardcoded `z-index: 9999` is a sign of z-index escalation, not a solution)
- Generate a modal that does not return focus to the trigger on close — screen reader users become disoriented
- Render the modal inline in the DOM (inside the triggering component's subtree) — always portal to `<body>` to prevent overflow clip
- Generate confirmation modals with two primary-emphasis buttons — the destructive or primary action gets primary emphasis, the dismiss gets secondary or ghost
- Generate non-dismissible modals for anything other than genuinely required user action (confirmation of data destruction, authentication step) — all informational modals must be dismissible
- Add additional `<body>` elements or nested scroll containers inside the modal without spec permission
- Auto-open a modal on page load without a user interaction trigger (autoplay accessibility violation)

### Prompt Seed

```
Generate a Modal component for [System Name].

Reference: SDDn Behavior Spec [MOD-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Variants required: informational, confirmation (with destructive action), form modal
Sizes: sm, md, lg
Framework: [React / Vue / HTML — specify]

Token constraints:
- Backdrop: color-surface-overlay at [opacity — per L1]
- Panel background: color-surface-raised
- Panel shadow: shadow-lg
- Panel radius: radius-lg
- Entry animation: motion-duration-standard + motion-ease-enter
- Exit animation: motion-duration-fast + motion-ease-exit
- Header padding: spacing-6
- Body padding: spacing-6
- Footer padding: spacing-4 spacing-6
- Max-width (sm/md/lg): [specify from L1 layout tokens or hard values per spec]

Component references:
- Buttons in footer: [BTN-L2-NNN] v[version]
- Form fields (form modal): [FORM-L2-NNN] v[version]

Accessibility (non-negotiable):
- role="dialog" + aria-modal="true"
- aria-labelledby → modal heading ID
- Focus trap: Tab cycles inside modal only
- Focus moves in on open, returns to trigger on close
- Escape closes (except non-dismissible alert variant)
- prefers-reduced-motion: no animation

Do not generate: inline-DOM modals (must portal to <body>), hardcoded z-index values, auto-open on page load.
```
