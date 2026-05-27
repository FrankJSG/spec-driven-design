# SDDn for Developers

SDDn gives you something that Figma files and design briefs rarely do: a machine-readable contract you can implement against without interpretation. This guide explains what SDDn expects from you and how to use specs effectively in your workflow.

---

## What the spec is

An ACTIVE .dsmd spec is an implementation contract. When you receive an L3 Delivery Spec with ACTIVE status, it tells you:

- Exactly which components to use and which variants (section 3: Components Used)
- The complete flow logic with all branches (section 4)
- How to handle every non-happy-path scenario (section 5: Edge Cases)
- What "done" looks like in testable terms (section 7: Definition of Done)

You are not expected to make design decisions from a .dsmd spec. Every ambiguity is a spec deficiency — raise it as a question in the spec review, not as an implementation assumption.

---

## Token consumption

Every L2 and L3 spec traces visual decisions to L1 token names. Your job is to consume those tokens as CSS custom properties (or equivalent in your system).

The L1 Foundation Spec provides the canonical token values. Your implementation layer maps spec token names to the system's CSS custom properties:

```css
/* Foundation: var(--color-action-primary): #3B6FF0 */
.btn-primary {
  background-color: var(--color-action-primary);
  color: var(--color-text-on-action);
  padding: var(--spacing-2) var(--spacing-4);
  border-radius: var(--radius-md);
  font: var(--typography-label-md);
  transition: background-color var(--motion-duration-fast) var(--motion-ease-standard);
}

.btn-primary:hover {
  background-color: var(--color-action-primary-dark);
}
```

No hex values. No `px` values that map to spacing tokens. If you find yourself writing `background: #3B6FF0` in production code, that is a spec contract violation.

**Why this matters:** When the L1 token changes (rebrand, accessibility fix, theme update), you update the token definition in one place. Every component that references the token updates automatically. This only works if implementations use token names, not token values.

---

## Reading an L2 Behavior Spec

The sections most relevant to your implementation:

**Section 3: States & Variants** — the complete list of what you need to build. If the spec defines 4 variants and 6 states, you implement 4 × 6 = 24 combinations. No more, no fewer. If a state exists in production but is not in the spec, the spec needs to be updated — do not implement undocumented states.

**Section 4: Token Usage** — the explicit mapping from property to token. Treat this as the definitive source; if there's a conflict between this table and the visual mockup, the spec wins. Flag the conflict to the spec owner.

**Section 5: Interaction Contract** — keyboard events, mouse events, async behavior. This section is often undertreated in traditional handoffs. The spec should tell you exactly which key activates the component, what happens on loading state timeout, and how error state recovery works.

**Section 8: Acceptance Criteria** — write your unit and integration tests against these. If a criterion is untestable, flag it to the spec owner before implementation. "All variants pass WCAG 2.2 AA" is testable with `axe`. "Feels premium" is not a spec criterion.

---

## Reading an L3 Delivery Spec

**Section 3: Components Used** — this is your component import list. All components used in the delivery are listed here with their L2 spec IDs. Before writing a line of code, verify every listed spec is ACTIVE.

**Section 4: Flow Logic** — implement each step as written. The branching paths in the flow logic are your state machine. If the spec says Step 2 → Step 3 on happy path, Step 2 → error handling on card decline, you implement both branches.

**Section 5: Edge Cases** — each edge case includes the component state to invoke. You know exactly which L2 spec's error state handles each scenario. You do not invent error handling — you apply the spec's.

**Section 7: Definition of Done** — run through this checklist before marking the ticket done. Some items are yours (no raw token values, no DRAFT spec references). Some items require QA. Some items require PO sign-off. Understand which is which.

---

## Spec gaps

When you encounter something in implementation that the spec does not cover:

1. **Do not invent a solution.** Invented solutions become undocumented behavior that future specs will conflict with.
2. **Flag it to the spec owner.** A spec gap is a spec problem, not an implementation problem.
3. **Document the gap.** Add a comment to your PR: "Spec ODS-BTN-L2-001 does not define behavior when the button is inside a disabled fieldset. Flagged to spec owner. Using `pointer-events: none` on fieldset as temporary handling."
4. **Spec gets updated.** The spec owner adds the missing edge case and the spec is re-reviewed if the change is significant.

This process creates a feedback loop between implementation and spec that improves the spec over time. Spec gaps found in Track B are one of the primary inputs into the EVOLVE stage of the lifecycle.

---

## AI agents and spec compliance

If your team uses AI agents to generate component code from specs, your role shifts:

**You are the validator**, not the generator. The agent generates; you verify the output against the spec's Acceptance Criteria.

Specifically:

- Run the acceptance criteria checklist against the generated output before merging
- Check for spec violations in the Forbidden section: raw hex values, unlisted variants, `outline: none` without replacement
- Verify the inheritance chain is resolved: if the component references `var(--color-action-primary)`, that property must be defined in the L1 spec and present in the CSS custom property system

A common agent failure mode: the agent generates visually correct output that violates the token contract. Something like:

```css
/* Agent-generated — VIOLATION */
.btn-primary {
  background: #3B6FF0; /* Should be var(--color-action-primary) */
}
```

This passes visual inspection but breaks the rebranding/theming guarantee. Catch it in validation.

---

## Spec states and your workflow

| Spec state | What you can do |
|-----------|----------------|
| DRAFT | Read for reference; cannot build from it |
| ACTIVE | Build from it; this is the contract |
| STALE | Do not build new work; in-flight work should pause or risk explicit acknowledgement |
| DEPRECATED | Do not use; check for replacement spec in `replacement_spec` field |

If you receive a ticket to implement something and the relevant spec is STALE, the ticket should not be started until the spec is re-reviewed and ACTIVE. This is not bureaucracy — a STALE spec means a parent constraint changed and the spec's accuracy is unknown.

---

## Version references in code

When you ship an implementation built from a spec, record the spec ID and version in the component documentation:

```tsx
/**
 * Button component.
 * @spec ODS-BTN-L2-001 v2.0.0
 */
export function Button({ variant, size, ...props }: ButtonProps) {
```

This creates a traceability link from code to spec. When the spec is updated to v2.1.0, you can grep the codebase for `ODS-BTN-L2-001` and find all implementations that reference the previous version — they are candidates for update review.

---

## Tech Lead review of L2 specs

If you are the Tech Lead reviewing an L2 Behavior Spec, your checklist focuses on:

- **Interaction Contract completeness** — are all keyboard events covered? Is async behavior specified to the level of "what happens on timeout"?
- **Acceptance Criteria testability** — can every item be verified with your current test tooling? If not, flag the criterion for revision.
- **Token implementability** — is every token name in the Token Usage table defined in the referenced L1? (A spec that references a non-existent token is a broken contract before implementation even starts)
- **AI Generation Rules consistency** — do the Generate and Forbidden sections avoid contradictions? A rule that says "generate all state variants" in Generate and then forbids some states in Forbidden needs clarification.

You are not evaluating design quality in a spec review. You are evaluating whether the spec is complete enough to implement from.
