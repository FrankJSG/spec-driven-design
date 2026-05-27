# Review Matrix

The review matrix defines who is accountable for approving specs at each layer, what they are evaluating, and the SLA for completing a review.

---

## Matrix Summary

| Layer | Spec Type | Primary Reviewer | Secondary Reviewer | SLA | Quorum |
|-------|-----------|------------------|--------------------|-----|--------|
| L1 | Foundation Spec | Design Lead | Product Owner | 48h | Both must approve |
| L2 | Behavior Spec | Senior Designer | Tech Lead | 24h | Both must approve |
| L3 | Delivery Spec | Product Owner | QA Lead | 24h | Both must approve |

Quorum is mandatory at all layers. A spec with one approval and one abstention remains in DRAFT.

---

## L1 Foundation Spec Review

### Reviewers

**Design Lead**
Accountable for the system's design coherence. Evaluates the spec against the product's design strategy and brand requirements. Has veto authority on token definitions and design principles.

**Product Owner**
Accountable for the product's business requirements. Evaluates accessibility baseline, brand constraints, and AI Generation Rules against product and legal obligations. Has veto authority on accessibility and compliance-adjacent decisions.

### What reviewers assess

**Design Lead checklist:**
- [ ] Design Principles are intent-based, not aesthetic-based
- [ ] Token Contract is complete and covers all required categories
- [ ] Token names follow the system's naming convention
- [ ] No token is redundant with an existing token
- [ ] Brand Constraints align with current brand guidelines
- [ ] AI Generation Rules are specific enough to constrain an agent

**Product Owner checklist:**
- [ ] Intent statement accurately describes the system's scope
- [ ] Accessibility Baseline meets legal and product standards
- [ ] Brand Constraints do not conflict with licensing or legal requirements
- [ ] Forbidden section in AI Generation Rules covers compliance-critical prohibitions
- [ ] Version and Ownership fields are complete and correct

### SLA: 48 hours

The 48h SLA begins when the spec author formally submits for review (e.g., opens a PR against `specs/review`).

SLA pauses if reviewers request changes. It restarts when the author resubmits.

**Why 48h for L1?** Foundation Spec changes are rare and high-impact. 48h allows reviewers to read carefully, consult stakeholders, and assess cascade impact on existing L2 specs.

---

## L2 Behavior Spec Review

### Reviewers

**Senior Designer**
Accountable for design system coherence at the component level. Evaluates whether the spec accurately describes how the component behaves across all states and variants, and whether it is consistent with adjacent components.

**Tech Lead**
Accountable for implementation feasibility and engineering quality. Evaluates whether the spec is implementable, whether the Interaction Contract is complete, and whether the Acceptance Criteria are technically testable.

### What reviewers assess

**Senior Designer checklist:**
- [ ] Intent statement correctly describes the component's purpose
- [ ] All necessary states and variants are defined (no gaps)
- [ ] No states or variants that contradict adjacent component specs
- [ ] Token Usage maps to real L1 tokens (no raw values, no invented tokens)
- [ ] Do / Don't pairs are accurate and explained
- [ ] Scope & Boundaries prevent over-generation by agents

**Tech Lead checklist:**
- [ ] Interaction Contract covers all keyboard and mouse events
- [ ] Acceptance Criteria are testable with existing test tooling
- [ ] Token Usage is implementable (no token that requires custom engineering)
- [ ] AI Generation Rules are unambiguous for the generating model
- [ ] `inherits_from` version is current

### SLA: 24 hours

24h SLA from formal submission. L2 reviews happen frequently (every new component, every significant update). The 24h window keeps the review queue from becoming a bottleneck.

---

## L3 Delivery Spec Review

### Reviewers

**Product Owner**
Accountable for business and user requirements. Evaluates whether the spec correctly captures what needs to be built and for whom.

**QA Lead**
Accountable for testability and quality standards. Evaluates whether the spec produces a testable delivery contract.

### What reviewers assess

**Product Owner checklist:**
- [ ] Intent aligns with approved product requirements
- [ ] User Context accurately describes the target user and their state
- [ ] Flow Logic covers all required business paths
- [ ] Edge Cases include all known user and system error scenarios
- [ ] Definition of Done reflects the agreed-upon business completion criteria
- [ ] All referenced L2 specs are ACTIVE (not DRAFT or STALE)

**QA Lead checklist:**
- [ ] Definition of Done is fully testable
- [ ] Validation Checklist covers all known regression areas
- [ ] Edge Cases have specific handling (not vague "show error" instructions)
- [ ] Flow Logic is complete enough to write a test plan from
- [ ] AI Generation Rules do not conflict with referenced L2 rules

### SLA: 24 hours

L3 specs are the most frequent review type. The 24h SLA must be protected operationally — a slow L3 review queue is a direct bottleneck on delivery velocity.

---

## Review Protocol

### Submission

Specs are submitted for review by creating a pull request (or equivalent in your tooling) against the `specs/review` branch. The PR must include:

- The .dsmd file
- A `review_request.md` with: spec type, layer, summary of changes, impact assessment
- For STALE → ACTIVE re-reviews: a diff summary showing what changed and why

### Comments and Feedback

Reviews are **async-first**. Reviewers leave comments directly on the .dsmd file. The author responds to each comment inline.

No synchronous review meetings are required for normal spec reviews. Synchronous discussion is appropriate when:
- A fundamental design decision is being contested
- A cross-L1 change needs alignment across multiple spec owners
- A spec is being rejected and the rejection reason requires explanation

### Approval

Approval is recorded as a PR approval (or equivalent). Both designated reviewers must approve. Approval means:

> "I have reviewed this spec against my checklist. It is complete, accurate, and safe to promote to ACTIVE."

Approval is not a rubber stamp. Reviewers are accountable for the specs they approve.

### Rejection

A spec is rejected (returned to DRAFT) when:
- A required section is incomplete or missing
- A fundamental accuracy problem is identified
- The spec contradicts an ACTIVE parent spec
- The AI Generation Rules are insufficient to constrain a generating agent
- The Acceptance Criteria or Definition of Done are not testable

Rejection must include a written reason. The author addresses the rejection reason and resubmits.

---

## Escalation

When reviewers disagree and cannot resolve asynchronously:

| Situation | Escalation path |
|-----------|----------------|
| L1 reviewers disagree | Chief Design Officer or equivalent resolves |
| L2 reviewers disagree | Design Lead + Tech Lead resolve (consult in 24h) |
| L3 reviewers disagree | Product Director resolves (48h) |

Escalations should be rare. Most review disagreements resolve with one round of author revision.

---

*Previous: [05-spec-health-system.md](05-spec-health-system.md) · Next: [07-lifecycle.md](07-lifecycle.md)*
