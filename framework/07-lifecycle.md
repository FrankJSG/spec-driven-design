# The SDDn Lifecycle

The full lifecycle of any design element governed by SDDn moves through seven stages. The lifecycle is circular — EVOLVE feeds back into SPEC — because a design system is never finished.

---

## Overview

```
    ┌──────────┐
    │ DISCOVER │
    └────┬─────┘
         │  Track A: exploration, research, ideation
         ▼
    ┌──────┐
    │ SPEC │ ◀──────────────────────────────────────────┐
    └──┬───┘                                            │
       │  .dsmd authored (DRAFT)                        │ feedback loop
       ▼                                                │
    ┌────────┐                                          │
    │ REVIEW │                                          │
    └────┬───┘                                          │
         │  DRAFT → ACTIVE (Review Gate)                │
         ▼                                              │
    ┌──────────┐  ◀─── STALE-ADVISORY: continues        │
    │ GENERATE │        with mandatory warning           │
    └────┬─────┘  ◀─── STALE: blocked until updated     │
         │  AI agent executes from ACTIVE spec          │
         ▼                                              │
    ┌──────────┐                                        │
    │ VALIDATE │                                        │
    └────┬─────┘                                        │
         │  Output checked against spec                 │
         ▼                                              │
    ┌──────┐                                            │
    │ SHIP │                                            │
    └──┬───┘                                            │
       │  Delivery in production                        │
       ▼                                                │
    ┌────────┐                                          │
    │ EVOLVE │ ─────────────────────────────────────────┘
    └────────┘
```

---

## Stage 1 — DISCOVER

**Track:** A (Discovery)
**Goal:** Understand the problem space well enough to write a spec.

### Activities

- User research: interviews, session recordings, analytics analysis
- Competitive and comparative analysis
- Design exploration: sketches, wireframes, low-fidelity prototypes
- Technical feasibility assessment
- Stakeholder alignment sessions
- AI-assisted ideation (as a thinking partner, not a production generator)

### Outputs

- Research synthesis documents
- Exploration artifacts (Figma, sketches — not production code)
- Decision log: the key design decisions made and why
- A clear enough understanding of the element to begin speccing

### Exit condition

Discovery ends when the team has enough shared understanding to write a Spec Draft. The test: can the spec author write the Intent section without guessing?

---

## Stage 2 — SPEC

**Track:** A → B transition
**Goal:** Translate discovery findings into a .dsmd Spec Draft.

### Activities

- Spec author fills out the appropriate template (L1, L2, or L3)
- References parent specs (existing L1 for an L2; existing L2s for an L3)
- Documents states, variants, flow logic, edge cases
- Writes AI Generation Rules with specific Generate / Validate / Forbidden / Prompt Seed
- Sets `status: DRAFT` and requests review

### Quality bar for submission

A Spec Draft is ready for review when:
- All required sections are filled (no TBDs in normative fields)
- Parent spec references are valid and ACTIVE
- AI Generation Rules are specific enough to constrain an agent
- The author can defend every decision with reference to discovery findings

### Common SPEC pitfalls

- Skipping states that seem obvious (obvious to the author, not to the agent)
- Leaving the Forbidden section vague or empty
- Inconsistency with the parent L1 token contract
- Acceptance Criteria that restate the spec rather than test it

---

## Stage 3 — REVIEW

**Track:** B entry gate
**Goal:** Promote the Spec Draft to ACTIVE via the designated reviewers.

### Process

See [Review Matrix](06-review-matrix.md) for the full protocol.

In brief:
1. Author opens review PR with .dsmd file and `review_request.md`
2. Designated reviewers review against their checklists within the SLA
3. Comments are addressed by the author inline
4. Both reviewers approve → `status: DRAFT` changes to `status: ACTIVE`
5. Spec is merged to `specs/active` branch
6. Downstream stakeholders notified

### What REVIEW is not

- Not a design critique session (design decisions were made in DISCOVER)
- Not a scope expansion opportunity (scope changes go back to SPEC)
- Not optional ("we'll review it after shipping" is a Track B violation)

---

## Stage 4 — GENERATE

**Track:** B
**Goal:** Produce implementation artifacts from the ACTIVE spec.

### AI generation workflow

1. Agent receives the spec (or a prompt seeded with the Prompt Seed from the spec)
2. Agent resolves the full inheritance chain: L3 → L2 references → L1
3. Agent checks the status of every spec in the chain before generating
4. Agent reads and applies AI Generation Rules from all layers
5. Agent produces output: component code, screen layout, flow scaffolding, copy
6. Agent annotates each generated element with the spec section it satisfies
7. Agent flags any spec gaps encountered during generation (missing states, ambiguous edge cases)

### Cascade events during GENERATE

A parent spec change can interrupt the GENERATE stage depending on its severity. The agent checks all spec states at the start of every generation task.

| Parent change type | Effect on GENERATE stage |
|-------------------|--------------------------|
| patch | No interruption. Generation proceeds normally. |
| minor | STALE-ADVISORY cascade. Generation proceeds. Agent prepends mandatory warning to all output. |
| major | STALE cascade. Generation is blocked. Agent returns STALE message and halts. |

**If STALE-ADVISORY during GENERATE:** the team decides whether to accept the warning and proceed, or pause to resolve the spec first. Either choice is valid. The warning is documented in the output so the decision is traceable.

**If STALE during GENERATE:** generation stops. The spec owner must resolve the STALE state (update the spec, re-review, promote to ACTIVE) before generation can resume. In-flight work that has already been generated from the spec should be flagged for re-validation once the spec is back to ACTIVE.

### Human-in-the-loop

Generation is not fully autonomous. The designer or tech lead reviews generated output before it moves to VALIDATE. They are checking spec compliance, not aesthetic preference (the spec governs aesthetics).

If generated output reveals a gap in the spec, the correct response is to update the spec (ACTIVE → DRAFT → revised → re-reviewed → ACTIVE) and regenerate. Not to deviate from the spec silently.

---

## Stage 5 — VALIDATE

**Track:** B
**Goal:** Confirm that generated and implemented output satisfies the spec contract.

### Validation types

**Spec validation** (designer / tech lead)
- Generated output matches every token value in section 4 (L2) or components used (L3)
- All states and variants render correctly
- AI Generation Rules were respected (nothing in Forbidden was generated)

**Acceptance validation** (QA)
- All Acceptance Criteria from L2 are met for the component
- All Definition of Done items from L3 are checked off for the flow
- Automated accessibility scan runs clean

**Inheritance validation**
- Output does not introduce decisions that contradict the L1 Foundation
- No raw values appear where tokens are required

### Validation failures

If validation fails:
- **Spec gap:** spec is missing a state or rule → update spec, regenerate, re-validate
- **Generation error:** agent violated spec → regenerate with corrected prompt, re-validate
- **Implementation error:** developer deviated from spec → fix implementation, re-validate
- **Spec error:** spec itself is wrong → spec enters revision cycle, implementation paused

---

## Stage 6 — SHIP

**Track:** B
**Goal:** Deliver the validated implementation to production.

### Ship conditions

All of the following must be true before shipping:
- Spec status is ACTIVE
- All VALIDATE checks passed
- No open spec gaps (or open gaps are documented as follow-up specs)
- QA sign-off recorded
- PO sign-off recorded

### What ships with the delivery

- The implemented component or feature
- A reference to the spec ID and version it was built from
- Any follow-up spec IDs for gaps found during the cycle
- Updated component documentation (if applicable)

---

## Stage 7 — EVOLVE

**Track:** A (for research) → returns to SPEC
**Goal:** Incorporate learning from production into the spec system.

### Triggers for EVOLVE

- User research reveals component behavior problems
- Analytics show pattern abandonment or friction
- Accessibility audit identifies L1 baseline failures
- Technical debt requires component refactoring
- Product strategy shift requires new component behaviors
- AI model improvement enables new generation capabilities

### EVOLVE activities

- Production analytics review
- User feedback synthesis
- A/B test result analysis
- Accessibility audit
- AI generation quality review (are agents drifting from spec intent?)

### EVOLVE outputs

- Updated specs (minor: PATCH version; significant: MINOR version → re-review required)
- New spec drafts for net-new elements discovered in production
- Deprecation decisions for elements that should be removed
- Updated AI Generation Rules when model behavior has changed

### The feedback loop

EVOLVE feeds back into SPEC. A design system that never evolves is a design system that is being abandoned. The lifecycle is designed to make evolution safe: cascading health states ensure that spec changes propagate correctly, and the review gate ensures that evolution decisions are validated before they affect production generation.

---

## Cascade Events in the Lifecycle

A cascade event is triggered when an L1 Foundation Spec releases a new version. The event type (patch, minor, major) determines how the cascade interrupts - or does not interrupt - the lifecycle for specs that inherit from it.

### patch change

A patch change fixes wording, spelling, or metadata in the L1. It does not alter any token value, principle scope, or rule.

**Effect:** No cascade. All inheriting L2 and L3 specs continue through the lifecycle without interruption. The spec owner may update `inherits_from.last_verified` at next review.

### minor change

A minor change adds new tokens, principles, or rules to the L1 without modifying existing ones.

**Effect:** STALE-ADVISORY cascade to all inheriting L2 specs. L3 specs that reference affected L2s receive a secondary cascade.

- Inheriting L2 specs transition from ACTIVE to STALE-ADVISORY
- AI agents may continue to generate from STALE-ADVISORY specs
- All generated output carries the mandatory STALE-ADVISORY warning
- Spec owners have 5 business days (L2) or 3 business days (L3) to verify impact and re-approve
- If the timebox expires: spec degrades to STALE, generation is blocked

The lifecycle for in-flight work continues with warnings. Teams may choose to pause and resolve the STALE-ADVISORY before shipping, or proceed and resolve it in the next spec cycle.

### major change

A major change eliminates, renames, or modifies an existing token value, or changes the scope of an existing design principle.

**Effect:** STALE cascade immediately to all inheriting L2 specs. All generation is blocked.

- Inheriting L2 specs transition from ACTIVE to STALE immediately
- AI agents cannot generate from STALE specs
- All in-flight Track B work that has not yet shipped must pause
- Spec owners must update affected sections, increment version, and re-review before resuming

The lifecycle pauses at GENERATE for all affected specs until they are restored to ACTIVE. This is intentional - a major L1 change means the constraints that governed the component have changed, and generated output from the old constraints may be incorrect.

### Cascade classification responsibility

The Design Lead classifies every L1 version change as patch, minor, or major before publishing. The classification checklist and cascade message formats are in [`framework/08-cascade-radar.md`](08-cascade-radar.md). The workflow for L1 maintainers is in [`CONTRIBUTING.md`](../CONTRIBUTING.md).

---

## Lifecycle Timing Reference

| Stage | Typical duration | Bottleneck risk |
|-------|-----------------|-----------------|
| DISCOVER | 1–2 weeks (new) / 1–3 days (iteration) | Research access |
| SPEC | 1–3 days | Spec author bandwidth |
| REVIEW | 24–48h per layer | Reviewer availability |
| GENERATE | Hours (AI) / days (human) | Spec completeness |
| VALIDATE | 1–2 days | QA queue |
| SHIP | Per team's release cadence | Release process |
| EVOLVE | Ongoing | Prioritization |

---

*Previous: [06-review-matrix.md](06-review-matrix.md) · Back to [README](../README.md)*
