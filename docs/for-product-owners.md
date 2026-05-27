# SDDn for Product Owners

SDDn gives you something most design frameworks do not: a testable definition of done that exists before a pixel is drawn. This guide explains where your work intersects with the framework and how to use it effectively.

---

## Why SDDn matters for POs

Design decisions are product decisions. When a button's label says "Submit" vs. "Save changes", that is a product decision. When an error message says "Something went wrong" vs. "Your payment failed. Check your card and try again", that is a product decision with user experience and support ticket implications.

SDDn makes these decisions explicit, reviewable, and versionable before they reach production. You stop discovering design problems in code review. You start catching them in spec review.

The investment is a 24-hour review SLA on L3 specs and co-authorship of the User Context and Definition of Done sections. The return is fewer revision cycles, fewer handoff misunderstandings, and AI agents that generate behavior matching your product requirements.

---

## Your role in each layer

### L1 Foundation Spec — Reviewer

You are a required reviewer for the L1 Foundation Spec alongside the Design Lead.

**Your focus areas:**

**Accessibility Baseline** — Does the stated WCAG conformance level meet your product's legal and compliance obligations? Does it meet the expectations of your user base? Enterprise B2B products in regulated industries have higher accessibility obligations than many consumer products. You are accountable for this.

**Brand Constraints** — The voice and tone rules in the Brand Constraints section govern how the system writes button labels, error messages, and empty state copy. Review these against your product's approved tone guidelines. "Errors describe what happened and what to do" vs. "Errors state the code and nothing else" is a product decision, not a design decision.

**AI Generation Rules — Forbidden section** — The Forbidden section is where you can encode product constraints that must not be violated by AI-generated output. If your product must never show pricing information in certain contexts, that goes here.

---

### L3 Delivery Spec — Primary Reviewer & Co-Author

This is where SDDn pays the most dividends for POs. You are a required reviewer on all L3 specs and often the co-author of key sections.

**Sections you own or co-own:**

**Intent** — You should be able to write this in one to two sentences. If you cannot, the scope of the L3 is not clear yet. "This spec governs the checkout flow for users upgrading from Trial to Pro. The goal is to minimize drop-off between plan selection and payment confirmation" is a clear intent. "This spec is for the billing screen" is not.

**User Context** — The anxiety / friction points section is especially important. What is this user afraid of at this exact moment in the product? What question do they need answered before they will take the action? These are product research findings. If your team has done user research on this flow, those findings belong here.

**Definition of Done** — You sign off on the Definition of Done. The items you add here are the business completion criteria: "PO sign-off: pricing, seat count, and billing date display are accurate." This is distinct from the technical criteria (QA's responsibility) and the accessibility criteria. You are checking that what was specced was actually built.

---

### Review: your checklist for L3 specs

When reviewing an L3 Delivery Spec as Product Owner:

- [ ] Intent aligns with approved product requirements (feature brief, OKR, ticket)
- [ ] User Context accurately describes the target user and their state (matches persona docs)
- [ ] Flow Logic covers all required business paths (not just happy path — all entry points, all branches)
- [ ] Edge Cases include all known user and system error scenarios (from research, from support tickets, from stakeholder input)
- [ ] Definition of Done reflects the agreed-upon business completion criteria
- [ ] All referenced L2 specs are ACTIVE (not DRAFT or STALE) — you should not approve a delivery spec that references unreviewed or outdated components
- [ ] The copy in the flow logic (button labels, headings, error messages) matches approved voice and tone

---

## The User Context section

This section is the most valuable thing you bring to an L3 spec. It is the difference between a spec that says "user wants to upgrade" and a spec that says:

> *User type: Authenticated Admin, Trial plan (7 days remaining). They have created 3 reports during their trial and are actively using the product. They are anxious about losing their reports if they do not upgrade, and they are uncertain about whether the charge happens today or when the trial ends. They are likely on a corporate card that may have restrictions they are not aware of.*

That context shapes every other section of the spec:
- The flow must show "when will I be charged?" prominently (anxiety point)
- The edge case for "3DS authentication required" goes in the spec (corporate card behavior)
- The confirmation screen must reassure the user that their trial reports are preserved (loss aversion)

An L3 spec without deep User Context is a wireframe description. An L3 spec with deep User Context is a delivery contract that an AI agent can execute against and a developer can build from.

---

## Edge cases are your responsibility too

Edge cases are often thought of as a developer or QA concern. In SDDn, they are a joint product and QA concern, and they belong in the spec before anyone writes code.

**Sources for edge cases:**

- User research findings (what did users try to do that broke?)
- Support tickets from similar flows in your product or competitors
- Stakeholder input ("What happens when the card is corporate and 3DS is required?")
- Data analysis (what percentage of users have corporate cards that trigger 3DS?)
- Known system constraints (what happens if Stripe is down?)

A common PO mistake: writing edge cases as vague fallbacks ("show an error message"). SDDn requires specific edge case handling:

```
Edge case: Card declined
Trigger: Stripe returns decline response
Handling: Error toast appears. Card number and CVV fields enter error state.
Component behavior: Button → Default. Toast → Error variant, 8s auto-dismiss.
Recovery path: User updates card details and retries.
```

Not: "Show an error if the card doesn't work."

---

## Definition of Done vs. Acceptance Criteria

These are different things in SDDn and they often get confused.

**Acceptance Criteria** (in L2 Behavior Spec) are per-component, technical, verified by QA:
- "Primary button renders with `color-action-primary` background in Default state"
- "Focus ring is visible on all variants"

**Definition of Done** (in L3 Delivery Spec) is per-delivery, functional and business, signed off by PO:
- "Pricing, seat count, and billing date are accurate in staging"
- "Confirmation email received at workspace billing email after successful upgrade"
- "Trial expiry banner dismissed system-wide after upgrade"

Your Definition of Done items should be things you personally verify before signing off. "QA passed" is not a PO Definition of Done item — that is QA's domain. "PO confirmed that the upgrade correctly changes the workspace plan status visible in Admin → Billing" is yours.

---

## Spec review as a product ritual

Treat L3 spec reviews as a lightweight sprint ritual, not a bureaucratic step. A well-written L3 review takes 30–45 minutes. A poorly-written L3 review that catches a fundamental misunderstanding about user context before development starts saves 2–5 days of revision.

The 24-hour SLA is not bureaucratic procedure — it is a forcing function that keeps spec reviews from becoming the bottleneck. A spec sitting in review for 3 days is a delivery blocked for 3 days.

If you find yourself repeatedly raising the same concerns in L3 reviews (missing edge cases, vague User Context, aspirational Definition of Done), run a brief spec-writing workshop with your team. The review matrix exists to catch problems — the goal is for those problems to become rare.
