# L3 — Delivery Spec

## Definition

An L3 Delivery Spec defines **what is delivered for a specific screen, flow, or feature, and how it is validated**. It is scoped to a single deliverable: one screen, one user flow, one feature surface. It does not redefine components — it declares which components are used and how they are assembled.

An L3 answers the question: *What exactly is being built here, for whom, under what constraints, and how do we know it is done?*

---

## Role in the Layer Hierarchy

```
L1 Foundation Spec
└── L2 Behavior Spec (referenced, not redefined)
    └── L3 Delivery Spec  ← You are here
```

L3 specs are the most frequently written layer. A design system with 40 components might have 4–10 L2 specs per component group and 50–200 L3 specs across the product.

---

## When to Write an L3

- Before designing any screen or flow that will be handed to a developer or AI agent
- Before generating UI from an AI agent — the L3 is the agent's instruction set
- When an existing screen changes significantly (flow logic change, new edge cases, new user segment)
- **Not** for minor copy or color updates — those cascade from L1 and L2 automatically
- **Not** for component changes — those require an L2 update

---

## The 9 Sections

### 1. Intent

One to two sentences stating what this screen or flow is for and what user or business problem it solves.

> Example: *This spec governs the checkout flow for paying users upgrading from Trial to Pro. The goal is to minimize drop-off between plan selection and payment confirmation.*

### 2. User Context

Who the user is when they arrive at this screen/flow, and what state they are in.

Required entries:
- **User type:** role, authentication state, plan, data state
- **Entry points:** how the user arrives here (from where, via what action)
- **Prior knowledge:** what the user is expected to know at this point
- **Goal:** what the user is trying to accomplish
- **Anxiety / friction:** known hesitations or blockers for this user at this point

This section is what separates a Delivery Spec from a wireframe annotation. It grounds the design in real user behavior.

### 3. Components Used

An explicit list of L2 specs this delivery relies on. Each entry references the spec by ID and version.

> Format:
> ```
> - Button (primary, secondary) — spec: BTN-L2-001 v2.1.0
> - FormInput (text, email, credit card) — spec: FRM-INPUT-L2-003 v1.4.0
> - Modal (confirmation) — spec: MOD-L2-002 v1.2.0
> - Toast (success, error) — spec: TOAST-L2-001 v1.0.0
> ```

No component behavior is re-described here. If a component behaves differently in this flow than its L2 spec defines, the L2 spec must be updated or a new L2 variant must be created.

### 4. Flow Logic

The complete logical sequence of the user flow, including branching paths. This is not a flowchart (no visual tool required) — it is a numbered, branching text description.

Required structure:
```
Step 1: [Screen/State name]
  - What is displayed
  - What the user can do
  - Condition → Step N (happy path)
  - Condition → Step N (error path)
  - Condition → Step N (alternative path)
```

Cover every screen and every state transition. The flow logic is what the AI agent uses to sequence generation tasks.

### 5. Edge Cases

Explicit handling for every non-happy-path scenario. An edge case without handling is a spec gap — and a production bug waiting to happen.

Required format per edge case:
```
Edge case: [description]
Trigger: [what causes this case]
Handling: [what the UI does]
Component behavior: [which L2 state is invoked]
```

Minimum required edge cases for any transactional flow:
- Network error / timeout
- Validation failure
- Session expiry
- Empty / no-data state
- Concurrent access (if applicable)

### 6. AI Generation Rules

Required subsections:

**Generate:** What an AI agent can produce from this L3 spec (screen layouts, flow scaffolding, copy, form validation logic, animation sequences).

**Validate:** What the agent must verify before approving generated output (component compliance against referenced L2 specs, flow completeness, edge case coverage, accessibility).

**Forbidden:** What the agent must never generate (components not listed in section 3, flow steps not in section 4, solutions to edge cases not defined in section 5, custom tokens).

**Prompt Seed:** A base prompt fragment that invokes this specific delivery spec when attached to a generation task. Should reference the L2 spec IDs so the agent resolves the full inheritance chain.

### 7. Definition of Done

The specific, testable criteria that define when this delivery is complete. This section is owned jointly by the PO and QA.

Distinct from Acceptance Criteria (which are per-component in L2) — this is per-delivery, covering the assembled flow.

> Example:
> ```
> - [ ] All screens render with no missing token values
> - [ ] Happy path completes end-to-end without errors in staging
> - [ ] All 5 edge cases in section 5 tested and handled per spec
> - [ ] No WCAG 2.2 AA violations in automated scan
> - [ ] PO sign-off on flow logic matches business intent
> - [ ] QA regression: no impact on adjacent flows
> ```

### 8. Validation Checklist

A structured checklist for the reviewer to complete before approving the L3. Different from Definition of Done (which is for the implementer) — this is for the spec reviewer.

```
Spec quality:
- [ ] All components referenced exist as ACTIVE L2 specs
- [ ] Flow logic covers all stated entry points
- [ ] All edge cases have explicit handling
- [ ] AI Generation Rules are non-contradictory with referenced L2 rules
- [ ] Definition of Done is fully testable

Content:
- [ ] Intent aligns with product requirements
- [ ] User context matches current persona / segment definition
- [ ] Copy in flow logic matches approved tone and voice from L1
```

### 9. Inherits From

References to all L2 specs this delivery uses (mirroring section 3, structured for machine readability).

```
inherits_from:
  - spec_id: BTN-L2-001
    version: 2.1.0
    layer: L2
  - spec_id: FRM-INPUT-L2-003
    version: 1.4.0
    layer: L2
```

---

## Schema Reference

The full .dsmd schema for L3 is in [`templates/delivery-spec.dsmd`](../templates/delivery-spec.dsmd).

A complete, filled example is in [`examples/checkout-flow/`](../examples/checkout-flow/).

---

## Common Mistakes

| Mistake | Why it breaks SDDn |
|---------|-------------------|
| Re-describing component behavior | Creates spec drift; L2 is the source of truth |
| Vague or aspirational Definition of Done | Spec never closes; QA has no contract |
| Missing edge cases | AI agents generate happy-path-only output; production gaps appear |
| Referencing DRAFT or STALE L2 specs | Agent operates on unvalidated or outdated rules |
| Writing L3 specs too broadly (multi-flow) | Scope becomes unmanageable; review SLA cannot be met |

---

*Previous: [02-behavior-spec.md](02-behavior-spec.md) · Next: [04-dual-track-model.md](04-dual-track-model.md)*
