# Examples

These are complete, production-ready .dsmd files for a fictional SaaS product called **Orbit** — a B2B analytics dashboard platform.

They are not templates. Every section is filled with real, specific content. They show the framework working in practice.

## The Orbit context

Orbit is an analytics platform for growth teams at mid-market SaaS companies. Its primary users are:

- **Analysts** — build reports, monitor metrics, investigate anomalies
- **Managers** — review dashboards, track team performance, share reports with stakeholders
- **Admins** — manage team access, configure data sources, set up alerts

The design system is called **Orbit Design System (ODS)**.

## Examples

### [`design-system-bootstrap/`](design-system-bootstrap/) — L1 Foundation Spec

The complete Orbit Design System Foundation Spec. Shows how to define tokens, brand constraints, accessibility baseline, and AI generation rules for a full product design system.

**Use this when:** Starting a new design system. This example shows the full scope of what an L1 spec covers and at what level of detail.

---

### [`component-primary-button/`](component-primary-button/) — L2 Behavior Spec

The Orbit primary button component, fully specced. Shows all variants, all states, token mappings, interaction contract, Do/Don't rules, and acceptance criteria.

**Use this when:** Writing your first L2 spec. This example demonstrates the level of precision expected — specific enough to constrain an AI agent, complete enough to replace a handoff document.

---

### [`checkout-flow/`](checkout-flow/) — L3 Delivery Spec

The Orbit plan upgrade flow — from plan selection through payment confirmation. Shows a realistic multi-step transactional flow with edge cases, user context, and definition of done.

**Use this when:** Writing an L3 spec for a transactional flow. This example shows the level of edge case coverage expected and how to reference L2 specs without re-describing their behavior.

---

## Reading guide

Each example can be read standalone. For maximum value, read them in order:

1. Read the L1 example to understand what the system-level constraints look like
2. Read the L2 example to see how a component spec inherits from those constraints
3. Read the L3 example to see how a delivery spec references components without repeating them

Notice what is **not** in each layer:
- The L1 does not describe component behavior
- The L2 does not describe tokens (it references them by name)
- The L3 does not describe component behavior (it references L2 specs by ID)

Inheritance keeps each layer focused on its own concern.
