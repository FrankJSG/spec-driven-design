# SDDn for Designers

SDDn changes where your primary artifact lives. The mockup is no longer the handoff document — the spec is. This guide explains what that means for how you work day-to-day.

---

## What changes

**Before SDDn:**
- You design a component in Figma
- A developer interprets your mockup
- An AI agent guesses at your intent
- You spend time in review correcting interpretations

**With SDDn:**
- You write a spec before or alongside your Figma work
- The spec is the handoff document — developers and agents build from it
- Your Figma file is a visual rendering of the spec, not the source of truth
- Review conversations reference the spec, not aesthetics

You are not doing more work. You are shifting work from reactive (review corrections) to proactive (spec authoring) — and that shift compounds over time.

---

## Your role in each layer

### L1 Foundation Spec

You are the primary author of your system's L1. You own the token contract, the design principles, and the AI generation rules.

**You need to answer:**
- What are the 4–5 principles that guide design decisions in this system? (Not rules — intent statements)
- What is the complete, canonical set of design tokens? (Color, typography, spacing, radius, shadow, motion)
- What constraints does brand place on this system?
- What can an AI agent generate from this system, and what must it never generate?

**Practical tip:** Start with your existing Figma token library or style guide. Translate it into the L1 Token Contract format. The key change is adding *intent* to every token — not just the value.

```
Before: primary-500: #3B6FF0
After:  color-action-primary: #3B6FF0
        Intent: Primary interactive affordance — CTAs, links, active states
```

The name encodes the role, not the appearance. This is what makes the token meaningful to an AI agent.

---

### L2 Behavior Spec

This is where most of your daily spec-writing happens. One L2 per component.

**The discipline SDDn requires:**
The spec must describe *behavior*, not just *appearance*. The question "what does this look like in disabled state?" is incomplete. The question is "what is the component doing in disabled state, what can the user do, and what ARIA attribute communicates this to assistive technology?"

A spec that only lists visual properties is a stylesheet with extra steps. A spec that also documents interaction contracts and intent is worth its authoring time.

**The section that most designers underwrite:** Do / Don't.

The Validate and Forbidden sections in AI Generation Rules are where your component expertise protects against agent errors. If you have ever said "that's not how this component is supposed to be used" in a code review, that rule belongs in the Forbidden section.

---

### L3 Delivery Spec

You author or co-author L3 specs with the Product Owner. You own the visual assembly decisions; the PO owns the user context and business logic.

**The section that most benefits from your design background:** Edge Cases.

Designers are usually the first to think about empty states, loading states, and error states in context. You already think in flows and branching paths. SDDn makes those implicit decisions explicit and forces them to be documented before the AI agent encounters them.

---

## Working with AI agents

With SDDn, your relationship with an AI agent changes:

**You are not**: giving the agent a creative brief and hoping for the best.

**You are**: giving the agent an ACTIVE spec and asking it to execute within defined constraints.

The Prompt Seed in your AI Generation Rules section is the invocation. Add it to any generation request for that component. The agent knows:
- What it can generate
- What it must validate before approving output
- What it is forbidden to do
- Which L1 tokens to use

When an agent produces output that violates the spec, that is information about your spec. Either the Forbidden section needs a new rule, or the Prompt Seed needs a tighter constraint.

---

## Discovery vs. speccing

SDDn does not require that you know everything before you design. Track A exists precisely because design thinking is iterative.

**In Track A (Discovery):** explore freely. Sketches, wireframes, explorations in Figma. The output of Track A is a Spec Draft.

**The transition:** when your exploration has converged on what this component or flow should be, write the spec. This is the point where you crystallize the decisions from discovery into a durable document.

**In Track B (Execution):** design from and to the spec. Figma is used to verify that the spec generates the right visual output, not to make new design decisions.

If you make a new design decision in Figma during Track B, that decision needs to go into the spec before it goes into production. "The Figma file was updated" is not a spec update.

---

## The Design Principles section of L1

This section deserves attention because it is where your design philosophy becomes machine-readable.

A good Design Principle is:
- An intent statement about design decisions, not a description of style
- Short enough to be referenced easily
- Specific enough to resolve ambiguous decisions

Examples:

| Too vague | Better |
|-----------|--------|
| Use whitespace generously | Reduce cognitive load by spacing related elements at 24px and unrelated elements at 48px |
| Be consistent | Every interactive element must look interactive; every non-interactive element must not |
| Keep it clean | A surface that can answer its primary question without scrolling is a good surface |

The test for a good principle: can you use it to settle a design argument? If "Clarity over density" resolves whether to show 3 metrics or 8 metrics on a KPI card, it is a good principle.

---

## Review: your role as Senior Designer on L2 reviews

When you review an L2 Behavior Spec as the Senior Designer, you are checking:

- **Completeness:** Are all the states real? Are any states missing? (Designers commonly forget the edge states — disabled, loading, error in context)
- **Accuracy:** Does the spec describe how the component actually behaves, or how the spec author wishes it would behave?
- **Consistency:** Does this component's behavior align with adjacent components in the system?
- **Specificity:** Are the AI Generation Rules specific enough to constrain an agent? Vague rules produce vague output.

You are not re-designing the component in review. You are verifying that the spec is a true description of the design intent.

---

## Common mistakes designers make in SDDn

| Mistake | What to do instead |
|---------|-------------------|
| Writing visual specs without intent | Every token mapping should note why that token, not just which token |
| Omitting the Forbidden section | List at least 3 things agents commonly get wrong for this component type |
| Treating Acceptance Criteria as aspirational | Write only criteria you can test today, not criteria for the ideal state |
| Starting L3 specs in Figma | Write the L3 before you open the Figma file — the spec informs the design, not the reverse |
| Updating Figma but not the spec | The spec is the source of truth; Figma follows the spec |
