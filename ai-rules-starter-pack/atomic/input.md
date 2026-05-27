# Input — AI Generation Rules

> Copy section 7 below into your Input L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Input L2 spec may produce:

- Input field component with all declared type variants: text, email, password, number, search, URL
- All interaction states: default (empty), filled, focused, hover, error, success, disabled, read-only
- Label element: always associated via `htmlFor` / `for` attribute — never floating label that requires JavaScript to function without it
- Helper text element: visible below input, linked via `aria-describedby`
- Error message element: conditionally visible, linked via `aria-describedby`, announced by screen reader on state change
- Character count indicator when `maxLength` is declared in the spec
- Password visibility toggle (eye icon) for password type, with `aria-label` on the toggle
- Prefix and suffix slot (icon or text) if declared in spec scope
- Controlled and uncontrolled usage examples
- Storybook stories covering: all types × all states × empty vs. filled × with and without helper text
- Validation pattern examples for email, required, and min/max length

### Validate

Before approving any generated Input output, verify:

- [ ] Border, text, and background values reference named system tokens — no raw values
- [ ] Focused state border applies `color-border-focus` token
- [ ] Error state applies `color-semantic-error` to border and error message text
- [ ] Error message is linked to input via `aria-describedby` — not just visually adjacent
- [ ] Label element is associated with input via `for` / `htmlFor` — not `aria-label` alone
- [ ] Disabled state uses `color-text-disabled` and blocks all interaction including copy
- [ ] Read-only state is distinct from disabled — content is selectable, interaction is blocked
- [ ] Password toggle, if generated, uses a proper `<button>` element, not a clickable icon
- [ ] Focus ring uses `color-border-focus` token, not a browser default or custom color
- [ ] Touch target height ≥ 44px
- [ ] Typography uses the system's form label and body tokens — no arbitrary font sizes

### Forbidden

An AI agent working on Input must never:

- Generate placeholder text as a substitute for a visible label — placeholder text is supplementary, not a label
- Use `autocomplete="off"` unless the spec explicitly requires it and states the reason
- Generate password inputs without a visibility toggle option (agents may ask if it's in scope, but may not omit it silently)
- Place error messages in `title` attributes or tooltips — they must be visible and in the DOM
- Generate `readonly` and `disabled` as visually identical — they are distinct states with distinct behavior
- Use `<div contenteditable>` as an input replacement
- Add browser-native validation styling without overriding it with system tokens
- Infer input type from a field name (e.g., auto-switching to `type="email"` because the field is labeled "Email") — type must be declared in the spec

### Prompt Seed

```
Generate an Input component for [System Name].

Reference: SDDn Behavior Spec [INPUT-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Types required: text, email, password (with visibility toggle), search
States: default (empty), filled, focused, error, disabled, read-only
Framework: [React / Vue / HTML — specify]

Token constraints:
- Border (default): color-border-default
- Border (focused): color-border-focus
- Border (error): color-semantic-error
- Label text: color-text-primary, typography-label-md
- Input text: color-text-primary, typography-body-md
- Helper text: color-text-secondary, typography-body-sm
- Error text: color-semantic-error, typography-body-sm
- Background: color-surface-base
- Disabled background: [specify]
- Padding: spacing-2 (vertical) × spacing-3 (horizontal)
- Radius: radius-md
- Focus ring: color-border-focus + shadow-focus

Accessibility:
- Label always associated via for/htmlFor
- Error message linked via aria-describedby
- Password toggle: <button> with aria-label="Show/hide password"
- Touch target height ≥ 44px

Do not generate: placeholder-as-label patterns, tooltip-based error messages, raw color values.
```
