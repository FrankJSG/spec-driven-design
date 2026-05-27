# Button — AI Generation Rules

> Copy section 7 below into your Button L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Button L2 spec may produce:

- All declared variants (primary, secondary, ghost, destructive, link) as styled components in the target framework (HTML/CSS, React, Vue, Svelte, or as specified)
- All interaction states for each variant: default, hover, focus, active, disabled, loading, error
- ARIA markup: `role="button"`, `aria-disabled`, `aria-busy` for loading state
- Keyboard interaction handler: Tab focus, Enter and Space activation
- Loading spinner that replaces label text during async operation, sized to maintain button height
- Icon + label layout variant (icon left, icon right) if icon support is in scope
- Size variants (sm, md, lg) if declared in the spec's States & Variants section
- Storybook or equivalent stories covering every variant × state matrix
- Unit test scaffolding for interaction contract (click, keyboard, disabled behavior)

### Validate

Before approving any generated Button output, verify:

- [ ] All background, text, and border color values reference named system tokens — no hex, RGB, or HSL literals
- [ ] All padding, min-width, and min-height values reference spacing tokens
- [ ] Border-radius references the system radius token (e.g., `radius-md`)
- [ ] Font references the system label token (e.g., `typography-label-md`)
- [ ] Transition duration and easing reference motion tokens
- [ ] Hover state applies the correct `-dark` or `-hover` variant of the action color token
- [ ] Focus state renders a visible focus ring using `color-focus-ring` and `shadow-focus` tokens
- [ ] Disabled state: `pointer-events: none` AND `aria-disabled="true"` present — not just `opacity` change
- [ ] Loading state: button is non-interactive while `aria-busy="true"` is active
- [ ] Touch target: computed height ≥ 44px in all size variants
- [ ] Destructive variant uses `color-semantic-error` tokens, not primary action tokens
- [ ] Each generated variant has a corresponding story / test

### Forbidden

An AI agent working on Button must never:

- Generate variants not declared in the L2 spec's States & Variants section (no ad hoc "warning" or "info" variants without spec coverage)
- Use raw color values (`#3B82F6`, `rgb(59, 130, 246)`) anywhere in the output
- Apply opacity-only disabled styling without also blocking pointer events and setting `aria-disabled`
- Generate a button with two conflicting primary-level styles on the same surface (e.g., two "primary" buttons side-by-side) — this is a usage violation, not a styling task
- Remove the focus ring under any condition, including a request to "clean up" styles
- Use `<div>` or `<span>` as the button element without explicit `role="button"` and keyboard handler
- Generate loading state that blocks all UI interaction beyond the button itself (loading is scoped to the button)
- Invent icon behavior not specified in the L2's Scope & Boundaries section

### Prompt Seed

```
Generate a Button component for [System Name].

Reference: SDDn Behavior Spec [BTN-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Variants required: primary, secondary, ghost, destructive
States per variant: default, hover, focus, active, disabled, loading
Framework: [React / Vue / HTML — specify]

Token constraints (all values must map to these named tokens):
- Background (primary default): color-action-primary
- Background (primary hover): color-action-primary-dark
- Text on primary: color-text-on-action
- Focus ring: color-focus-ring + shadow-focus
- Disabled text: color-text-disabled
- Padding: spacing-2 (vertical) × spacing-4 (horizontal)
- Radius: radius-md
- Font: typography-label-md
- Transition: motion-duration-fast + motion-ease-standard

Accessibility requirements:
- Tab focusable, Enter and Space to activate
- aria-disabled="true" on disabled state (not HTML disabled attribute alone)
- aria-busy="true" on loading state
- Minimum touch target: 44×44px

Do not generate: unlisted variants, raw hex values, div-as-button without role/keyboard handler.
```
