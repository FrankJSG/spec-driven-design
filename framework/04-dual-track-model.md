# The Dual-Track Model

SDDn operates on two parallel tracks. Understanding which track you are on — and why — is fundamental to the framework. Conflating them is the most common source of SDDn process failures.

---

## Overview

```
┌─────────────────────────────────┐   ┌─────────────────────────────────┐
│  TRACK A — Discovery            │   │  TRACK B — Execution            │
│                                 │   │                                 │
│  Free exploration               │   │  Spec-first, structured         │
│  No spec required               │   │  Requires ACTIVE spec           │
│  Output: Spec Draft             │   │  Output: Validated delivery     │
│                                 │   │                                 │
│  Figma explorations ✓           │   │  AI-generated components ✓      │
│  User research synthesis ✓      │   │  Developer implementation ✓     │
│  Concept sketches ✓             │   │  QA validation ✓                │
│  Competitive analysis ✓         │   │                                 │
│                                 │   │  "Just build it" ✗              │
│  AI-generated production UI ✗   │   │  Undocumented decisions ✗       │
└─────────────────────────────────┘   └─────────────────────────────────┘
           │                                        ▲
           │   Spec Draft → ACTIVE promotion        │
           └────────────────────────────────────────┘
```

---

## Track A — Discovery

### Purpose

Track A exists because design thinking is inherently exploratory. SDDn does not require that every creative act begin with a spec — it requires that anything delivered to production has been specced. Discovery is the process of arriving at a spec.

### What happens in Track A

- Designers explore freely: sketches, wireframes, Figma explorations, mood boards
- POs conduct user research and synthesize findings
- Teams run design sprints, competitive analyses, stakeholder interviews
- AI agents may be used as thinking partners and ideation tools
- Prototypes are built and tested

All of this is explicitly **not production work**. It is input to the speccing process.

### Track A outputs

Track A has exactly one formal output: a **Spec Draft**.

A Spec Draft is a .dsmd file in DRAFT status. It contains the spec author's best current understanding of what the element should be. It may be incomplete, uncertain, or contain open questions. The DRAFT state communicates: *this is not yet a contract — do not build from this*.

Track A ends when a Spec Draft is submitted for review.

### What AI agents may do in Track A

- Assist with research synthesis
- Generate ideation variants for human review
- Help write Spec Draft content
- Run checks against L1 for early constraint awareness

**AI agents may NOT generate production output in Track A.** No shipped component or screen should trace its lineage to Track A work without having passed through spec review.

---

## Track B — Execution

### Purpose

Track B is where SDDn's investment in specs pays off. Every build decision in Track B is governed by a spec. AI agents operate from ACTIVE specs with defined constraints. Developers implement against an explicit contract. QA validates against acceptance criteria.

### Entry condition

**A component or flow enters Track B only when its Spec Draft has been reviewed and promoted to ACTIVE status.**

There is no exception to this rule. If the spec is not ACTIVE, the work is still in Track A.

### What happens in Track B

1. AI agent receives the ACTIVE L3 Delivery Spec (plus referenced L2 and L1)
2. Agent generates output constrained by the spec's AI Generation Rules
3. Designer or tech lead reviews generated output against the spec
4. Iteration happens within the spec's boundaries (not by modifying the spec)
5. QA validates against the L3's Definition of Done
6. Delivery ships

### What AI agents do in Track B

AI agents in Track B operate as **executors with constraints**:
- They resolve the full inheritance chain before generating (L3 → L2 → L1)
- They apply all AI Generation Rules from all three layers
- They flag spec gaps (missing edge cases, ambiguous states) rather than inventing answers
- They do not generate anything listed in any layer's `Forbidden` section
- They produce output that references spec section numbers for traceability

### Track B constraints

| Rule | Reason |
|------|--------|
| No spec, no build | Prevents undocumented design debt |
| Spec must be ACTIVE, not DRAFT or STALE | DRAFT = unreviewed; STALE = possibly outdated |
| Spec changes during build require re-review | Mid-build spec edits break the contract for agents and developers |
| Deviations from spec are spec update requests, not implementation choices | Keeps the spec as the source of truth |

---

## The Transition: Track A → Track B

### Promotion process

1. Spec author submits Spec Draft for review (DRAFT → in review)
2. Designated reviewers per the [Review Matrix](06-review-matrix.md) assess the spec
3. Reviewers approve, request changes, or reject
4. On approval: spec status changes from DRAFT to ACTIVE
5. Component or flow moves to Track B

### What reviewers are looking for

- Completeness: all sections filled, no TBDs in normative fields
- Accuracy: intent aligns with product requirements
- Consistency: no contradiction with parent L1/L2 specs
- Testability: Acceptance Criteria and Definition of Done are verifiable
- AI readiness: AI Generation Rules are specific enough to constrain an agent

### Rejection scenarios

A spec can be rejected (returned to DRAFT) for:
- Missing required sections
- Contradiction with an ACTIVE parent spec
- Untestable criteria
- AI Generation Rules that contradict parent layer rules
- Scope that should be split across multiple specs

---

## Dual-Track in Practice

### Typical flow for a new component

```
Week 1-2: Track A
  Designer explores button variants in Figma
  Identifies 4 variants, 6 states
  Drafts BTN-L2 spec

Week 2: Transition
  Spec submitted for review
  Design Lead and Tech Lead review in 24h
  Two changes requested, author updates
  Spec promoted to ACTIVE

Week 2-3: Track B
  AI agent generates component variants from ACTIVE spec
  Developer reviews against spec
  QA validates acceptance criteria
  Button ships
```

### Typical flow for a new feature (uses existing components)

```
Week 1: Track A
  PO + designer align on checkout flow intent
  User research informs edge cases
  L3 Delivery Spec draft written
  References existing ACTIVE L2 specs

Week 1: Transition
  PO and QA review L3 in 24h
  Spec promoted to ACTIVE

Week 2: Track B
  AI agent generates checkout flow layout from L3 + referenced L2s
  Developer implements, QA validates DoD
  Checkout ships
```

---

*Previous: [03-delivery-spec.md](03-delivery-spec.md) · Next: [05-spec-health-system.md](05-spec-health-system.md)*
