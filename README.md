# SDDn — Spec Driven Design

> **SDDn (Spec Driven Design)** is a framework that transforms design — a historically interpretive discipline — into a system of structured, versionable, and AI-executable specifications, without sacrificing creative intent.

![Version](https://img.shields.io/badge/version-0.2.0-blue)
![Status](https://img.shields.io/badge/status-Early%20Access%20%2F%20Community%20RFC-yellow)
![License](https://img.shields.io/badge/license-MIT-green)

---

## The Problem

Design decisions live in Figma comments, Slack threads, and designers' heads. When a developer builds from a mockup, they interpret. When an AI agent generates UI, it guesses. When a spec exists, it lives in a PDF that no one updates.

The result: inconsistency at scale, rework on every handoff, and AI tools that produce plausible output with no guarantee of intent alignment.

SDDn exists because **a design without a spec is a wish, not a contract**.

---

## The 3-Layer Constitution

Every SDDn-governed system is defined by three nested layers of specification:

```
┌─────────────────────────────────────────────────────┐
│  L1 — Foundation Spec                               │
│  What are the immutable rules of the system?        │
│  tokens · brand · accessibility · AI constraints    │
│                                                     │
│  ┌───────────────────────────────────────────────┐  │
│  │  L2 — Behavior Spec                          │  │
│  │  How does each piece of the system behave?   │  │
│  │  states · variants · interactions · rules    │  │
│  │                                               │  │
│  │  ┌─────────────────────────────────────────┐ │  │
│  │  │  L3 — Delivery Spec                    │ │  │
│  │  │  What is delivered and how validated?  │ │  │
│  │  │  flows · edge cases · definition done  │ │  │
│  │  └─────────────────────────────────────────┘ │  │
│  └───────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

Each layer **inherits** from the one above. An L3 Delivery Spec cannot contradict an L2 Behavior Spec. An L2 cannot override an L1 Foundation. Inheritance is enforced, not assumed.

---

## The 5 Principles

| # | Principle | What it means |
|---|-----------|---------------|
| 1 | **Spec before pixels** | No element is built, generated, or shipped without a prior spec |
| 2 | **Intent over aesthetics** | The spec defines *why* a design decision was made, not only *what* it looks like |
| 3 | **Inheritance over repetition** | L3 inherits from L2, L2 from L1 — no duplicating rules that already live in a parent layer |
| 4 | **AI as executor, human as author** | Agents generate output from specs; designers make authoring decisions |
| 5 | **Living specs** | A spec without a version is dead documentation — all specs carry state and history |

---

## Quick Start

**Step 1 — Define your system foundation (L1)**

Copy [`templates/foundation-spec.dsmd`](templates/foundation-spec.dsmd) and fill in your design tokens, brand constraints, and accessibility baseline. This is your L1 Foundation Spec. It governs everything below it.

**Step 2 — Spec each component (L2)**

For every component your system needs, create an L2 Behavior Spec from [`templates/behavior-spec.dsmd`](templates/behavior-spec.dsmd). Define its states, variants, and interaction contract. Reference your L1 in the `inherits_from` field.

**Step 3 — Specify every delivery (L3)**

Before building any screen or flow, write an L3 Delivery Spec from [`templates/delivery-spec.dsmd`](templates/delivery-spec.dsmd). Reference the L2 specs your flow uses. This is the document your AI agent and your developer both work from.

> See [`docs/getting-started.md`](docs/getting-started.md) for a complete walkthrough.

---

## Repository Map

```
sddn-framework/
├── framework/          Core methodology documents
├── templates/          .dsmd spec templates (copy and fill)
├── ai-rules-starter-pack/  Ready-to-use AI generation rules by component
├── examples/           Complete, realistic filled .dsmd examples
└── docs/               Role-specific guides
```

### Framework Documents

| File | Contents |
|------|----------|
| [`framework/00-principles.md`](framework/00-principles.md) | The 5 SDDn principles, in depth |
| [`framework/01-foundation-spec.md`](framework/01-foundation-spec.md) | L1 definition, schema, and guidance |
| [`framework/02-behavior-spec.md`](framework/02-behavior-spec.md) | L2 definition, schema, and guidance |
| [`framework/03-delivery-spec.md`](framework/03-delivery-spec.md) | L3 definition, schema, and guidance |
| [`framework/04-dual-track-model.md`](framework/04-dual-track-model.md) | Discovery vs Execution tracks |
| [`framework/05-spec-health-system.md`](framework/05-spec-health-system.md) | Spec states and lifecycle transitions |
| [`framework/06-review-matrix.md`](framework/06-review-matrix.md) | Who reviews what and at what SLA |
| [`framework/07-lifecycle.md`](framework/07-lifecycle.md) | The full SDDn cycle end-to-end |
| [`framework/08-cascade-radar.md`](framework/08-cascade-radar.md) | L1 version classification and cascade message formats |

### What's new in v0.2.0

**1 - STALE-ADVISORY: a fifth spec state between ACTIVE and STALE**

The previous 4-state model treated all parent spec changes equally: any version increment made a child spec STALE and blocked AI generation. v0.2.0 introduces STALE-ADVISORY for minor parent changes (additive only, no breaking changes). Specs in STALE-ADVISORY remain operational - agents continue to generate, but all output carries a mandatory warning. A fixed timebox (5 business days for L2, 3 for L3) gives spec owners a window to verify impact and re-approve. Only if the timebox expires does the spec degrade to STALE. Major parent changes still go directly to STALE.

**2 - SemVer cascade semantics for L1**

A 4-question checklist in [`framework/08-cascade-radar.md`](framework/08-cascade-radar.md) gives the Design Lead an objective method for classifying every L1 change as patch (no cascade), minor (STALE-ADVISORY), or major (STALE). The cascade message format is typed - `[STALE-ADVISORY]` and `[STALE]` messages have distinct fields so spec owners and CI tooling can parse and act on them reliably.

**3 - Closed Dual-Track with Minimum Viable Spec Checklist**

The original Track A had no hard stop. A spec could cycle through review indefinitely. v0.2.0 defines a maximum of two async review iterations before mandatory live session escalation. Review Gates use a 5-point objective checklist (the Minimum Viable Spec) to replace subjective completeness judgments. Specs that pass 5/5 but have known gaps are promoted to ACTIVE with observations, which become `spec-observation` GitHub Issues - traceable and owned, not invisible.

---

### Role-Specific Docs

| Audience | Guide |
|----------|-------|
| Designers | [`docs/for-designers.md`](docs/for-designers.md) |
| Product Owners | [`docs/for-product-owners.md`](docs/for-product-owners.md) |
| Developers | [`docs/for-developers.md`](docs/for-developers.md) |
| Everyone | [`docs/getting-started.md`](docs/getting-started.md) |

---

## Status

SDDn v0.2.0 adds the STALE-ADVISORY state, SemVer cascade semantics, and the closed Dual-Track model with Minimum Viable Spec Checklist. See [CHANGELOG.md](CHANGELOG.md) for the full diff from v0.1.0.

The framework is published under a **Community RFC** model: read it, use it, and open issues to challenge or extend it. See [CONTRIBUTING.md](CONTRIBUTING.md) for how to contribute. No backward-compatible contract exists until v1.0.0 locks the schema.

---

## Author

**Francisco Javier Saavedra González**
Creator and principal author of SDDn.

---

## License

[MIT](LICENSE) — use freely, credit appreciated.
