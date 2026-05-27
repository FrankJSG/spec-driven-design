# Getting Started with SDDn

This guide walks you through adopting SDDn for the first time. It takes about 3–4 hours to complete your first L1 spec and have a functional spec-driven workflow in place.

---

## Prerequisites

You need:
- An existing or new design system to govern (even a minimal one)
- At least one designer and one developer who will participate in the spec review process
- A shared document or file system where .dsmd files will live (GitHub repo, Notion, Confluence — anywhere with versioning)

You do not need:
- Special tooling — .dsmd files are plain text with YAML front matter
- A complete design system — you can start SDDn with partial coverage and grow it
- Everyone on your team to buy in before starting — SDDn works incrementally

---

## Step 1 — Write your L1 Foundation Spec

Your L1 is the root of everything. Start here.

**Copy the template:**

```
templates/foundation-spec.dsmd → [your-repo]/specs/drafts/[system-name]-foundation.dsmd
```

**Fill in these sections first** (the rest can follow):

1. **Intent** — two sentences on what system this governs and what product it serves
2. **Token Contract** — your current design tokens, or a minimal set to start (color + spacing is enough for v0.1)
3. **Accessibility Baseline** — your WCAG target and minimum requirements
4. **Version & Ownership** — your name, today's date, status: DRAFT

**Defer these until you have the basics:**
- AI Generation Rules — add these after you've written your first L2 and understand what you need to constrain
- Brand Constraints — add as you identify cases where brand rules need to live in the spec

**Set status to DRAFT.** Submit it for review once all required sections are filled.

---

## Step 2 — Write your first L2 Behavior Spec

Pick a component your team ships repeatedly and often argues about. Good first candidates: Button, Input, Card. Avoid starting with highly complex components (DataTable, RichTextEditor) — they will take longer and you want a quick first win.

**Copy the template:**

```
templates/behavior-spec.dsmd → [your-repo]/specs/drafts/[component-name].dsmd
```

**Fill in these sections:**

1. **Intent** — what this component is for, why it exists
2. **States & Variants** — list every state and variant you actually ship (don't over-scope)
3. **Token Usage** — map every visual property to a token name from your L1
4. **Acceptance Criteria** — write these as checkboxes a QA person can verify
5. **Inherits From** — reference your L1 spec_id

The quality bar: could a developer build this component from this spec alone, without asking you any questions? If yes, it's ready for review.

---

## Step 3 — Run your first review

**For your L1:**
Your Design Lead and Product Owner need to review it. Share the .dsmd file with them and ask them to comment using the checklist in [`framework/06-review-matrix.md`](../framework/06-review-matrix.md). Target: done within 48 hours.

**For your L2:**
Your Senior Designer and Tech Lead review it. Same process. Target: 24 hours.

On approval, change `status: DRAFT` to `status: ACTIVE` and record the reviewers in `reviewed_by`.

---

## Step 4 — Use your first spec with an AI agent

Now you have an ACTIVE spec. Use it.

Take the Prompt Seed from your L2's AI Generation Rules section and add it to your agent prompt. Ask the agent to generate the component. Compare the output against your Acceptance Criteria checklist.

If the agent produces output that violates the spec, the problem is one of three things:
1. The Prompt Seed was too vague — make the constraints more explicit
2. The Forbidden list was incomplete — add the violation type to Forbidden
3. The Acceptance Criteria were ambiguous — tighten the spec

This is the calibration process. Most teams get it right by the second or third spec.

---

## Step 5 — Write your first L3 Delivery Spec

Pick a screen or flow your team is about to design or redesign. Write an L3 before you open Figma.

```
templates/delivery-spec.dsmd → [your-repo]/specs/drafts/[flow-name].dsmd
```

**The most important sections to nail in your first L3:**

1. **User Context** — who is this person, what are they trying to do, what are they afraid of
2. **Components Used** — list only components that have ACTIVE L2 specs; if a component is missing its L2, write that first
3. **Edge Cases** — this is where most teams underinvest; add at least 4 edge cases per flow

**Once ACTIVE:** give it to your AI agent and ask it to generate the screen. The output will be more consistent, more complete, and require fewer revision cycles than working from a design brief alone.

---

## File organization

A common starting structure:

```
[your-repo]/
└── specs/
    ├── drafts/          Active drafts under review
    ├── active/          All ACTIVE specs — the source of truth
    ├── stale/           STALE specs awaiting re-review
    └── deprecated/      Historical archive
```

Automate the movement between folders if possible. At minimum, use a git branch strategy: `specs/active` is the protected branch where only reviewed specs land.

---

## What success looks like at 30 days

- 1 ACTIVE L1 Foundation Spec
- 3–5 ACTIVE L2 Behavior Specs (your most-used components)
- 2–3 ACTIVE L3 Delivery Specs (recently designed flows)
- Your AI agent produces first-draft components that pass acceptance criteria without manual style corrections
- Your team has one less meeting because handoff questions have spec answers

---

## What to read next

| If you are... | Read |
|---------------|------|
| A designer | [`for-designers.md`](for-designers.md) |
| A product owner | [`for-product-owners.md`](for-product-owners.md) |
| A developer | [`for-developers.md`](for-developers.md) |
| Building your L1 | [`framework/01-foundation-spec.md`](../framework/01-foundation-spec.md) |
| Building your first L2 | [`framework/02-behavior-spec.md`](../framework/02-behavior-spec.md) |
| Running a review | [`framework/06-review-matrix.md`](../framework/06-review-matrix.md) |
| Looking at complete examples | [`examples/README.md`](../examples/README.md) |
