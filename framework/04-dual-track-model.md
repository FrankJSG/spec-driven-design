# The Dual-Track Model

SDDn operates on two parallel tracks. Understanding which track you are on - and why - is fundamental to the framework. Conflating them is the most common source of SDDn process failures.

---

## Overview

```
TRACK A - Discovery (fixed timebox by spec type)
     │
     ├─ Timebox 1 expires
     │         │
     │    Review Gate 1
     │         ├── 5/5 checklist ──▶ ACTIVE + observations ──▶ TRACK B
     │         └── < 5/5 ──▶ DRAFT + feedback + Timebox 2 (reduced)
     │                                    │
     │                          Timebox 2 expires
     │                                    │
     │                             Review Gate 2
     │                                    ├── 5/5 ──▶ ACTIVE + observations ──▶ TRACK B
     │                                    └── < 5/5 ──▶ Live Session (30 min)
     │                                                        │
     │                                              Reviewer documents output
     │                                              Designer validates
     │                                              Issues opened in repo
     │                                                        │
     └──────────────────────────────────────────────▶ ACTIVE + observations ──▶ TRACK B
```

Track A has a hard stop. A spec cannot cycle through more than two async review iterations. The third path is always a live session, after which the spec goes directly to ACTIVE with observations.

---

## Discovery Timeboxes

Each spec type has a fixed timebox for Track A. The timebox begins when the author opens the review request. A reduced second timebox applies after the first rejection.

| Spec type | Timebox 1 | Timebox 2 (reduced) | Escalation |
|-----------|-----------|---------------------|------------|
| L1 Foundation | 2 days | 1 day | 30-min live session |
| L2 Atomic | 2h | 1h | 30-min live session |
| L2 Molecular | 4h | 2h | 30-min live session |
| L2 Organism | 8h | 4h | 30-min live session |
| L3 Flow | 3 days | 1 day | 30-min live session |

Timeboxes are measured in calendar time from the moment the review request is submitted. Reviewers are expected to complete their review within the timebox. If a reviewer has not responded by the end of Timebox 1, the review is automatically escalated to Timebox 2.

---

## Minimum Viable Spec Checklist

The review gate is objective, not subjective. A spec passes Review Gate 1 or Gate 2 if and only if all five criteria are met. No partial credit.

- [ ] Inheritance from L1 (or L2 if L3) declared?
- [ ] Main states or variants listed?
- [ ] At least one edge case documented?
- [ ] AI Generation Rules has Generate and Forbidden (minimum)?
- [ ] At least one Prompt Seed present?

**If 5/5:** spec is a candidate for ACTIVE with observations. The reviewer documents what is missing or incomplete as GitHub Issues labeled `spec-observation`. The spec is promoted to ACTIVE.

**If < 5/5:** spec returns to DRAFT. The reviewer records exactly which criteria failed and why. The author addresses those criteria only in the next iteration.

This checklist is the minimum bar, not the ideal bar. A spec that passes 5/5 can still be incomplete in ways that matter for production use. Those gaps become `spec-observation` issues, visible and trackable, rather than blockers to progress.

---

## Review Gate: ACTIVE with Observations

A spec promoted with observations is fully ACTIVE. AI agents may operate on it. Developers may implement from it. The observations are not conditions - they are known gaps documented as GitHub Issues.

Observations must be:
- Opened as GitHub Issues with the label `spec-observation`
- Linked to the spec's `spec_id` in the issue body
- Assigned to the spec owner for resolution
- Resolved before the spec's next scheduled review

An observation is not a defect that blocks production. It is a documented known gap that the team has agreed to carry temporarily. If an observation would materially affect the correctness of generated output, it must be a rejection criterion, not an observation.

---

## Live Session Protocol

Live sessions are the final escalation path when a spec has failed two async review cycles.

**Who participates:** the spec author, the designated reviewer(s), and optionally the Tech Lead or Design Lead.

**Duration:** 30 minutes, hard stop.

**During the session:**
- The reviewer drives the discussion, not the author
- Decisions are made in real time
- The reviewer (not the author) documents all outputs in writing during the session

**After the session:**
1. Reviewer submits the documented output as a PR against the spec draft
2. Author validates the documentation is accurate
3. Author updates the spec to reflect the session decisions
4. Any gaps or open questions are opened as GitHub Issues with `spec-observation`
5. Spec is promoted to ACTIVE

The spec never returns to DRAFT after a live session. The live session is the final Track A iteration. If the author cannot produce a 5/5 spec after a live session, the spec's scope is too large and must be split.

---

## Track A — Discovery

### Purpose

Track A exists because design thinking is inherently exploratory. SDDn does not require that every creative act begin with a spec - it requires that anything delivered to production has been specced. Discovery is the process of arriving at a spec.

### What happens in Track A

- Designers explore freely: sketches, wireframes, Figma explorations, mood boards
- POs conduct user research and synthesize findings
- Teams run design sprints, competitive analyses, stakeholder interviews
- AI agents may be used as thinking partners and ideation tools
- Prototypes are built and tested

All of this is explicitly **not production work**. It is input to the speccing process.

### Track A outputs

Track A has exactly one formal output: a **Spec Draft**.

A Spec Draft is a .dsmd file in DRAFT status. It contains the spec author's best current understanding of what the element should be. It may be incomplete, uncertain, or contain open questions. The DRAFT state communicates: this is not yet a contract - do not build from this.

Track A ends when a Spec Draft is submitted for review.

### What AI agents may do in Track A

- Assist with research synthesis
- Generate ideation variants for human review
- Help write Spec Draft content
- Run checks against L1 for early constraint awareness

**AI agents may NOT generate production output in Track A.** No shipped component or screen should trace its lineage to Track A work without having passed through a Review Gate.

### L1 Foundation: what "sufficient" means

L1 specs have a different readiness threshold from L2 and L3 specs because a complete L1 is a prerequisite for writing any L2. A minimum viable L1 Foundation Spec is sufficient for Review Gate 1 when:

- Token Contract has at least the primary color tokens declared (action-primary, text-primary, surface-base, border-default, semantic error/success/warning/info)
- Minimum 3 Design Principles are declared as intent statements (not style rules)
- Accessibility baseline states a WCAG conformance level explicitly
- AI Generation Rules section contains at least a Generate list and a Forbidden list

A full L1 spec - with the complete token contract, all brand constraints, and a complete AI rules section - is the goal. The minimum viable threshold allows teams to begin L2 work while the L1 is completed in parallel, provided the minimum fields are in place.

---

## Track B — Execution

### Purpose

Track B is where SDDn's investment in specs pays off. Every build decision in Track B is governed by a spec. AI agents operate from ACTIVE specs with defined constraints. Developers implement against an explicit contract. QA validates against acceptance criteria.

### Entry condition

**A component or flow enters Track B only when its Spec Draft has passed a Review Gate and been promoted to ACTIVE status.**

There is no exception to this rule. If the spec is not ACTIVE, the work is still in Track A.

### What happens in Track B

1. AI agent receives the ACTIVE L3 Delivery Spec (plus referenced L2 and L1)
2. Agent resolves the full inheritance chain and checks all spec states
3. Agent generates output constrained by the spec's AI Generation Rules
4. Designer or tech lead reviews generated output against the spec
5. Iteration happens within the spec's boundaries (not by modifying the spec)
6. QA validates against the L3's Definition of Done
7. Delivery ships

### What AI agents do in Track B

AI agents in Track B operate as **executors with constraints**:
- They resolve the full inheritance chain before generating (L3 → L2 → L1)
- They check spec states: ACTIVE proceeds, STALE-ADVISORY proceeds with warning, STALE blocks
- They apply all AI Generation Rules from all three layers
- They flag spec gaps (missing edge cases, ambiguous states) rather than inventing answers
- They do not generate anything listed in any layer's `Forbidden` section
- They produce output that references spec section numbers for traceability

### Track B constraints

| Rule | Reason |
|------|--------|
| No spec, no build | Prevents undocumented design debt |
| Spec must be ACTIVE or STALE-ADVISORY | DRAFT = unreviewed; STALE = blocked |
| STALE-ADVISORY requires warning on all output | Parent change may affect generated output |
| Spec changes during build require re-review | Mid-build spec edits break the contract for agents and developers |
| Deviations from spec are spec update requests, not implementation choices | Keeps the spec as the source of truth |

---

## Dual-Track in Practice

### Typical flow for a new atomic component

```
L2 Atomic timebox: 2h (Timebox 1), 1h (Timebox 2)

Hour 0-2: Track A
  Designer drafts Button L2 spec (2h timebox running)
  Spec reaches 5/5 on MVS Checklist

Review Gate 1:
  Senior Designer + Tech Lead review within 2h
  2 observations noted (loading state detail, keyboard focus spec)
  Spec passes 5/5 - promoted to ACTIVE + 2 observations opened as Issues

Track B begins immediately:
  AI agent generates Button variants from ACTIVE spec
  Developer reviews against spec
  QA validates acceptance criteria
  2 observation Issues resolved in next spec version
```

### Typical flow for a spec that needs two iterations

```
L2 Organism timebox: 8h (Timebox 1), 4h (Timebox 2)

Hours 0-8: Track A - first attempt
  Designer drafts DataTable L2 spec
  Spec fails MVS checklist on Gate 1: missing edge cases, no Forbidden section
  Returned to DRAFT with exact failures noted

Hours 8-16: Track A - second attempt (4h Timebox 2)
  Author adds edge cases and Forbidden section
  Spec passes 5/5 at Review Gate 2
  Promoted to ACTIVE + 3 observations

Track B begins.
```

### Escalation to live session

```
L3 Flow timebox: 3 days (Timebox 1), 1 day (Timebox 2)

Days 0-3: Track A - first attempt
  PO + designer write Checkout L3
  Fails Gate 1: no edge cases, incomplete flow logic

Days 3-4: Track A - second attempt (1 day Timebox 2)
  Author updates but still fails Gate 2 on edge cases

Day 4: Live session (30 min)
  Reviewer documents edge cases and flow branches in real time
  Author validates documentation
  Spec promoted to ACTIVE + observations as Issues

Track B begins. No further Track A cycles possible for this spec.
```

---

*Previous: [03-delivery-spec.md](03-delivery-spec.md) · Next: [05-spec-health-system.md](05-spec-health-system.md)*
