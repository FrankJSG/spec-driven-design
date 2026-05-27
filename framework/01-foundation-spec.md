# L1 — Foundation Spec

## Definition

An L1 Foundation Spec defines the **immutable rules of a design system**. It is the contract that every component, pattern, and screen in the system must honor. There is typically one L1 per product or design system. Multiple products sharing a brand may share an L1 or extend it.

An L1 answers the question: *What are the non-negotiable constraints that everything else must respect?*

---

## Role in the Layer Hierarchy

```
L1 Foundation Spec  ← You are here
└── L2 Behavior Spec (inherits from L1)
    └── L3 Delivery Spec (inherits from L2, transitively from L1)
```

L1 is the root of the inheritance tree. When an L1 changes, all L2 specs that reference it cascade to STALE. This is not a bug — it is the system ensuring no component operates on outdated constraints.

---

## When to Write an L1

- When starting a new design system
- When onboarding a new product into an existing system (they may create an L1 that extends the parent)
- When a system-wide rebrand, accessibility audit, or platform migration requires a new set of immutable constraints
- **Not** when updating a single component — that is an L2 concern

---

## The 7 Sections

### 1. Intent

One to three sentences stating what this design system is for and what it governs. This is not marketing copy. It is the scope declaration.

> Example: *This Foundation Spec governs the design system for Orbit, a B2B SaaS analytics platform. It defines the constraints that all UI components, screens, and AI-generated output must respect.*

### 2. Design Principles

Three to seven intent-anchored principles that define how design decisions should be made within this system. These are not the SDDn framework principles — they are this system's design principles.

> Format: `[Name]: [Intent statement]`
> Example: *Clarity over density: Data dashboards should reduce cognitive load, not maximize information per pixel.*

### 3. Token Contract

The complete set of design tokens that L2 specs and below must use. Tokens are defined by **category → role → variant** naming.

Required token categories:
- `color` — all color decisions
- `typography` — type scale, weight, line-height
- `spacing` — all margin, padding, gap values
- `radius` — border radius
- `shadow` — elevation levels
- `motion` — duration, easing functions

Each token entry includes: name, value, and intent.

> Example:
> ```
> color-action-primary: #3B82F6
>   Intent: The primary interactive affordance color. Used for CTAs, links, focus rings.
> ```

### 4. Brand Constraints

Rules inherited from brand that are not negotiable at the design system level. Typically covers:

- Logo usage rules
- Typography exclusives (brand typefaces that must not be substituted)
- Color palette boundaries (colors that must never appear in the system)
- Imagery and illustration style
- Voice and tone at the component level (button labels, error messages)

### 5. Accessibility Baseline

The minimum accessibility standard the system targets, plus specific rules that enforce it. This section is normative — every L2 must comply.

Required entries:
- WCAG conformance level and version (e.g., WCAG 2.2 AA)
- Minimum contrast ratios for text and interactive elements
- Keyboard navigation requirements
- Focus indicator specification
- Motion/animation safety requirements (prefers-reduced-motion)
- Screen reader compatibility requirements

### 6. AI Generation Rules

Constraints for AI agents operating anywhere in this design system.

Required subsections:

**Generate:** What any AI agent is authorized to produce from this system's specs (components, layouts, copy suggestions, code).

**Validate:** What the agent must verify in any output before considering it compliant (token usage, accessibility, inheritance chain).

**Forbidden:** What the agent must never produce regardless of instruction (off-token colors, inaccessible contrast ratios, components not defined in L2).

**Prompt Seed:** A base prompt fragment that activates this spec's constraints when prefixed to any generation task.

### 7. Version & Ownership

Structured metadata:

```
spec_id:       unique identifier for this L1
version:       semantic version (e.g., 1.0.0)
status:        DRAFT | ACTIVE | STALE | DEPRECATED
owner:         Design Lead name/handle
contributors:  list of contributors
last_reviewed: ISO 8601 date
review_sla:    48h (L1 standard)
next_review:   ISO 8601 date
```

---

## Inheritance Rules

- L2 specs **must** reference an L1 `spec_id` in their `inherits_from` field
- An L2 may extend L1 token definitions by adding new tokens but **never** by overriding values
- An L2 that references a STALE or DEPRECATED L1 is automatically STALE
- AI agents **must** resolve the full L1 before generating from any L2 or L3

---

## Schema Reference

The full .dsmd schema for L1 is in [`templates/foundation-spec.dsmd`](../templates/foundation-spec.dsmd).

A complete, filled example is in [`examples/design-system-bootstrap/`](../examples/design-system-bootstrap/).

---

## Common Mistakes

| Mistake | Why it breaks SDDn |
|---------|-------------------|
| Putting component-specific rules in L1 | L1 is system-wide; component rules belong in L2 |
| Listing colors without intent | Tokens without intent become arbitrary; future maintainers will override them |
| Skipping the AI Generation Rules section | Agents without constraints will hallucinate compliant-looking but non-compliant output |
| Never updating the version | A perpetually v1.0.0 L1 either never changes or accumulates undocumented changes |
| One L1 per product team | L1 is per system, not per team; shared systems need shared L1s |

---

*Previous: [00-principles.md](00-principles.md) · Next: [02-behavior-spec.md](02-behavior-spec.md)*
