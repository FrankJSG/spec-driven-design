# L2 — Behavior Spec

## Definition

An L2 Behavior Spec defines **how a single design system element behaves**. "Element" covers any named, reusable piece of a system: a component (Button, Modal, DataTable), a pattern (empty state, error state, loading skeleton), or a compound element that has a stable identity.

An L2 answers the question: *How does this piece of the system behave across all contexts it may appear in?*

---

## Role in the Layer Hierarchy

```
L1 Foundation Spec (parent — must be ACTIVE)
└── L2 Behavior Spec  ← You are here
    └── L3 Delivery Spec (references this L2)
```

L2 specs are the core working layer of SDDn. A mature design system has one L2 per component. An L3 Delivery Spec references L2 specs rather than re-describing component behavior.

---

## When to Write an L2

- When adding a new component to the design system
- When a component's behavior changes significantly (new states, removed variants, interaction model change)
- When an existing component has been operating without a spec (spec-first applies retroactively)
- **Not** for one-off screen-specific variations — those belong in L3
- **Not** for token changes — those belong in L1

---

## The 9 Sections

### 1. Intent

One to two sentences stating what this component is for and what problem it solves. Not a visual description — a behavioral one.

> Example: *The Primary Button is the system's highest-emphasis call-to-action trigger. It communicates the most important available action on a given surface and should appear at most once per view.*

### 2. Scope & Boundaries

What this spec covers and, explicitly, what it does not cover. Boundaries prevent scope creep in review and prevent AI agents from over-generating.

> Example: *Scope: all button variants (primary, secondary, ghost, destructive) and all interaction states. Out of scope: icon buttons (see Button-Icon L2), link-style buttons (use typography tokens directly).*

### 3. States & Variants

Every state and variant this component can be in, with the visual and behavioral specification for each.

Required structure per variant:

```
Variant name
├── Visual spec (tokens used, layout rules)
├── When to use
├── When NOT to use
└── Difference from [related variant]

State name (default, hover, focus, active, disabled, loading, error)
├── Visual change from default
├── Behavioral change (what interaction is allowed)
└── Accessibility requirement for this state
```

### 4. Token Usage

Explicit mapping of design decisions to L1 tokens. Every color, spacing, typography, and motion value must reference a token name — never a raw value.

> Format:
> ```
> Background (default):    color-action-primary
> Background (hover):      color-action-primary-dark
> Text:                    color-text-on-action
> Padding (h):             spacing-3
> Padding (v):             spacing-2
> Border-radius:           radius-md
> Font:                    typography-label-md
> Focus ring:              color-focus-ring, shadow-focus
> Transition:              motion-duration-fast, motion-ease-standard
> ```

No raw hex values, no arbitrary spacing values. If a required value is not in L1 tokens, the L1 spec must be updated first.

### 5. Interaction Contract

The complete behavioral contract: what this component does in response to user interactions. This section is the source of truth for both developers and AI agents generating interaction code.

Cover:
- Mouse events (hover, click, focus, blur)
- Keyboard events (Tab, Enter, Space, Escape, Arrow keys where applicable)
- Touch events (tap, long press where applicable)
- Loading and async behaviors (what happens while an action is pending)
- Error and success state transitions

### 6. Do / Don't

A minimum of four Do / Don't pairs with explanations. Each pair must state *why* the rule exists, not just what it is.

> Example:
> ```
> DO:   Use Primary Button for the single most important action per view
> WHY:  Multiple primary buttons dilute emphasis and force users to parse priority
>
> DON'T: Use Primary Button for navigation ("Back", "Cancel")
> WHY:  Navigation is not an action; use secondary or ghost to signal lower emphasis
> ```

### 7. AI Generation Rules

Required subsections:

**Generate:** What an AI agent can produce from this L2 spec (component markup, state variants, interactive prototypes, documentation).

**Validate:** What the agent must check before approving any generated output (token compliance, state completeness, inheritance chain integrity, accessibility).

**Forbidden:** What the agent must never generate (off-spec variants, raw color values, custom states not defined in section 3, combinations that violate the Do / Don't rules).

**Prompt Seed:** A base prompt fragment that invokes this specific component spec when attached to a generation request.

### 8. Acceptance Criteria

Testable criteria that any implementation (human or AI-generated) must satisfy to be considered compliant with this spec.

> Format: Gherkin-style or plain checklist
> ```
> - [ ] All 5 states render with correct token values
> - [ ] Keyboard focus is visible with spec-defined focus ring
> - [ ] Disabled state prevents click and form submission
> - [ ] Loading state shows spinner and locks interaction
> - [ ] Touch target meets 44×44px minimum
> - [ ] Color contrast passes WCAG 2.2 AA in all states
> ```

### 9. Inherits From

Reference to the parent L1 spec.

```
inherits_from:
  spec_id: [L1 spec_id]
  version: [L1 version this L2 was written against]
  last_verified: [ISO 8601 date]
```

---

## Inheritance Rules

- An L2 **must** reference an ACTIVE L1 in `inherits_from`
- Token values in section 4 must map to tokens defined in the referenced L1
- When the referenced L1 changes version, this L2 enters STALE automatically
- An L2 may define component-scoped tokens (e.g., `button-min-width`) that extend, not override, L1 tokens

---

## Schema Reference

The full .dsmd schema for L2 is in [`templates/behavior-spec.dsmd`](../templates/behavior-spec.dsmd).

A complete, filled example is in [`examples/component-primary-button/`](../examples/component-primary-button/).

---

## Common Mistakes

| Mistake | Why it breaks SDDn |
|---------|-------------------|
| Specifying visual values instead of token names | Decouples spec from the L1 contract; breaks when tokens change |
| Omitting states (especially disabled and error) | AI agents generate incomplete components; edge cases break in production |
| Writing Acceptance Criteria that can't be tested | Untestable criteria are opinions, not contracts |
| One L2 per team instead of per component | Creates conflicting specs for the same component |
| Vague Scope & Boundaries | AI agents over-generate; reviewers approve the wrong things |

---

*Previous: [01-foundation-spec.md](01-foundation-spec.md) · Next: [03-delivery-spec.md](03-delivery-spec.md)*
