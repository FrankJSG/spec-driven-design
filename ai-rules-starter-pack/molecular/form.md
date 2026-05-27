# Form — AI Generation Rules

> Copy section 7 below into your Form L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Form L2 spec may produce:

- Form container with `<form>` element and correct `action` / `method` attributes (or `onSubmit` handler for SPA context)
- Field assembly: each field composed of Label + Input (or other control) + Helper text + Error message per the system's Input L2 spec
- Required field indicators: asterisk or "Required" label — per the spec's chosen pattern, consistently applied
- Inline validation: error messages appearing on field blur (not on submit-only), using error state from the relevant L2 control spec
- Submit-level error summary: an error summary region at the top of the form that lists all validation failures when submission is attempted, linked to each field via `href="#field-id"`
- Form states: idle, submitting (all fields and submit button disabled), success, error (server-level)
- Field grouping with `<fieldset>` and `<legend>` for related fields (address blocks, payment details, checkbox groups)
- Auto-layout grid: two-column for wider forms, single-column for narrow or mobile forms — per spacing tokens

### Validate

Before approving any generated Form output, verify:

- [ ] Every `<input>`, `<select>`, and `<textarea>` has an associated `<label>` via `for` / `htmlFor` — not `aria-label` alone
- [ ] Required fields have a visible indicator AND an `aria-required="true"` attribute
- [ ] Error messages are linked via `aria-describedby` to the field they describe
- [ ] Error summary region has `role="alert"` or `aria-live="assertive"` so it is announced on submission attempt
- [ ] Error summary links target the corresponding field by ID
- [ ] Form submitting state: all fields are `aria-disabled="true"`, submit button shows loading state (per Button L2 spec)
- [ ] `<fieldset>` and `<legend>` used for all grouped controls (checkboxes, radio groups, address blocks)
- [ ] No field relies solely on placeholder text as its label
- [ ] Gap between fields uses the form's declared spacing token — not arbitrary values
- [ ] All control components (inputs, selects, checkboxes) reference their respective ACTIVE L2 specs

### Forbidden

An AI agent working on Form must never:

- Generate forms that submit on field change without explicit user intent (no auto-submit on select change unless the spec declares it)
- Show validation errors only on final submit — inline validation on blur is required
- Generate error messages that only change the field's border color without text explanation (color-only error indication violates WCAG 1.4.1)
- Use `<table>` layout for form fields (forms are not tabular data)
- Disable the submit button as the primary validation feedback mechanism — the button may be disabled pre-first-submit, but must enable after first attempt so users can resubmit after fixing errors
- Generate a `<form>` without a submit mechanism (keyboard users must be able to submit without a mouse)
- Place error messages in `title` attributes or browser `alert()` dialogs
- Generate terms-of-service or consent checkboxes pre-checked

### Prompt Seed

```
Generate a Form component for [System Name].

Reference: SDDn Behavior Spec [FORM-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Form type: [e.g., sign-up, settings, checkout — specify]
Fields: [list field names and types]
Validation: inline on blur + submit-level error summary
Framework: [React / Vue / HTML — specify]

Component references (all must be ACTIVE):
- Input fields: [INPUT-L2-NNN] v[version]
- Submit button: [BTN-L2-NNN] v[version]
- [Additional components]

Token constraints:
- Field gap: spacing-6
- Section gap (fieldset to fieldset): spacing-8
- Label: typography-label-md, color-text-primary
- Helper text: typography-body-sm, color-text-secondary
- Error text: typography-body-sm, color-semantic-error
- Required indicator: color-semantic-error

Accessibility:
- All labels associated via for/htmlFor
- aria-required="true" on required fields
- Error messages via aria-describedby
- Error summary: role="alert", links to fields by ID
- Fieldset + legend for grouped controls

Do not generate: auto-submit on change, color-only error states, pre-checked consent boxes.
```
