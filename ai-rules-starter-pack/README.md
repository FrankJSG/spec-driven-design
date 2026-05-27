# AI Rules Starter Pack

Ready-to-use AI Generation Rules for common design system components, organized by atomic design level.

## What this is

Each file in this pack contains a complete `AI Generation Rules` section for its component type — the exact section you need to populate in your L2 Behavior Spec. These are not templates with blanks to fill. They are working rules, written for production use.

Copy the rules for a component, paste them into your L2 spec's section 7, and customize:
- Replace `[System Name]` with your product name
- Replace `[SYSTEM-L1-NNN]` with your Foundation Spec ID
- Adjust the forbidden list for your system's specific constraints
- Update the Prompt Seed to reference your actual spec IDs

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
