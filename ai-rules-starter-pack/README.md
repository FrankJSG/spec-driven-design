# AI Rules Starter Pack

Ready-to-use AI Generation Rules for common design system components, organized by atomic design level.

## What this is

Each file in this pack contains a complete `AI Generation Rules` section for its component type — the exact section you need to populate in your L2 Behavior Spec. These are not templates with blanks to fill. They are working rules, written for production use.

Copy the rules for a component, paste them into your L2 spec's section 7, and customize:
- Replace `[System Name]` with your product name
- Replace `[SYSTEM-L1-NNN]` with your Foundation Spec ID
- Adjust the forbidden list for your system's specific constraints
- Update the Prompt Seed to reference your actual spec IDs

## Universal Base Rule

This rule is mandatory in every AI Generation Rules section, at every layer (L1, L2, L3), without exception. It must appear in the Forbidden section of every spec you write, and in the Validate checklist.

---

**Universal Base Rule:**

> If a design value is not declared in the Token Contract of the inherited L1 spec, the agent must:
> 1. Stop
> 2. Report the ambiguous value with its context
> 3. NOT proceed until explicit instruction is received
>
> This rule takes precedence over any other rule in the spec.

---

This converts the Token Contract into an implicit whitelist. Any value not explicitly authorized in the L1 Token Contract is implicitly forbidden. The agent does not invent token values, approximate values from context, or use raw literals as a fallback. It stops and reports.

**Why this matters:** An agent that approximates a missing token produces output that looks correct but is not governed by the spec. Over time, these approximations accumulate as invisible design debt. The Universal Base Rule ensures that every undeclared value surfaces as a visible gap rather than a silent assumption.

**How to include it in your spec:** In the Forbidden section of your AI Generation Rules:

```
- Any design value not declared in the Token Contract of [SYSTEM-L1-NNN]. If a required value is missing from the Token Contract, stop, report the gap with its context, and wait for explicit instruction. This takes precedence over all other rules.
```

In the Validate checklist:

```
- [ ] All values in generated output map to named tokens in [SYSTEM-L1-NNN]. No approximated, inferred, or raw values present.
```

---

## How to use

**Quick path (80% of the time):**
1. Pick the file that matches your component
2. Copy the entire `AI Generation Rules` section
3. Paste into your L2 spec at section 7
4. Replace placeholder tokens with your actual L1 token names
5. Add your spec ID to the Prompt Seed

**Customization path (when the defaults don't fit):**
1. Use the file as a starting point
2. Edit the Generate list to add/remove what agents should produce for your context
3. Edit Forbidden to add system-specific constraints
4. Verify the Validate checklist maps to your L1's Accessibility Baseline

## Component index

### Atomic

| Component | File | Use when |
|-----------|------|---------|
| Button | [`atomic/button.md`](atomic/button.md) | Any interactive button element |
| Input | [`atomic/input.md`](atomic/input.md) | Single-line text input fields |
| Badge | [`atomic/badge.md`](atomic/badge.md) | Status indicators, count displays, labels |

### Molecular

| Component | File | Use when |
|-----------|------|---------|
| Card | [`molecular/card.md`](molecular/card.md) | Content containers with visual separation |
| Form | [`molecular/form.md`](molecular/form.md) | Multi-field form assemblies |
| Modal | [`molecular/modal.md`](molecular/modal.md) | Dialogs, confirmations, overlays |

### Organism

| Component | File | Use when |
|-----------|------|---------|
| Header | [`organism/header.md`](organism/header.md) | Top navigation bars |
| Sidebar | [`organism/sidebar.md`](organism/sidebar.md) | Side navigation panels |
| Data Table | [`organism/data-table.md`](organism/data-table.md) | Tabular data with sorting, filtering, pagination |

### Template

| Component | File | Use when |
|-----------|------|---------|
| Landing Page | [`template/landing-page.md`](template/landing-page.md) | Marketing or feature landing screens |
| Dashboard | [`template/dashboard.md`](template/dashboard.md) | Data-heavy authenticated product screens |
| Onboarding Flow | [`template/onboarding-flow.md`](template/onboarding-flow.md) | Multi-step user setup or activation flows |

## Important

These rules are written to be safe and conservative. An AI agent constrained by these rules will produce compliant, token-anchored output that requires less review correction. If you want an agent with more creative latitude, remove items from the Forbidden list deliberately and document why.

Never remove the Validate checklist. It is the minimum safety net for output review.

Never remove the Universal Base Rule from the Forbidden section. It is the floor beneath the entire spec system.
