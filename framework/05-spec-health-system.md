# Spec Health System

A spec's state is not metadata — it is a workflow control. The health system ensures that agents and developers always know whether a spec is trustworthy enough to build from.

---

## The Four States

```
  ┌─────────┐    review approved    ┌────────┐
  │  DRAFT  │ ───────────────────▶ │ ACTIVE │
  └─────────┘                       └────────┘
       ▲                                │  │
       │  updated +                     │  │ parent spec
       │  re-approved                   │  │ changes
       │                                │  ▼
  ┌────────┐                       ┌─────────┐
  │ ACTIVE │ ◀─ re-approved ─────  │  STALE  │
  └────────┘                       └─────────┘
       │
       │ component removed
       ▼
  ┌────────────┐
  │ DEPRECATED │
  └────────────┘
```

---

## State Definitions

### DRAFT

**Meaning:** The spec is being authored or revised. It has not been reviewed and approved.

**Who can work from it:** No one builds from a DRAFT. It may be read and discussed, but it is not a contract.

**AI agent access:** Read-only for reference. AI agents must NOT generate production output from DRAFT specs.

**Transitions out:**
- → ACTIVE: Designated reviewer(s) approve the spec after review
- Stays DRAFT: Reviewer requests changes; author revises and resubmits

**Metadata requirements:**
```yaml
status: DRAFT
version: [semver — can be 0.x.x]
owner: [spec author handle]
review_requested: [ISO 8601 date or null]
```

---

### ACTIVE

**Meaning:** The spec has been reviewed, approved, and is the authoritative definition for this element. Builds may proceed.

**Who can work from it:** Designers, developers, AI agents, and QA. This is the production-grade contract.

**AI agent access:** Full — agents may generate output from ACTIVE specs, constrained by the spec's AI Generation Rules.

**Transitions out:**
- → STALE: A parent spec (L1 or L2) that this spec inherits from changes version
- → STALE: A linked dependency (referenced component in section 3 of L3) updates to an incompatible version
- → DEPRECATED: The component or feature this spec governs is intentionally removed
- → DRAFT: A significant change is required; spec enters revision cycle (version is incremented)

**Metadata requirements:**
```yaml
status: ACTIVE
version: [semver — 1.x.x or higher for first ACTIVE state]
owner: [spec owner handle]
last_reviewed: [ISO 8601 date]
reviewed_by: [reviewer handle(s)]
next_review: [ISO 8601 date — typically 90 days from last_reviewed]
```

---

### STALE

**Meaning:** The spec was valid but a dependency has changed. It may no longer accurately describe how the element should behave given current system constraints.

**Who can work from it:** No one builds from a STALE spec without explicit risk acknowledgement. Ongoing in-flight work should pause pending re-review.

**AI agent access:** Blocked — agents must refuse to generate from STALE specs and surface the stale status to the requester.

**How a spec becomes STALE:**
- Automatic cascade: the L1 spec referenced by an L2 changes version
- Automatic cascade: an L2 spec referenced by an L3 changes version
- Manual trigger: a reviewer marks a spec as requiring re-evaluation (e.g., after a product pivot)
- Scheduled trigger: `next_review` date passes without re-review

**Transitions out:**
- → ACTIVE: Spec owner reviews the changed parent, updates the spec as needed, re-submits for review, review approved
- → DEPRECATED: On re-review, the element is determined to be removed

**The re-review process for STALE:**
1. Spec owner receives notification (or detects cascade)
2. Owner reviews the parent spec changes and assesses impact
3. If no impact: owner updates `inherits_from.last_verified` and `last_reviewed`, resubmits for review
4. If impact: owner updates affected sections, increments version, resubmits for review
5. Reviewer approves → ACTIVE

**Metadata requirements:**
```yaml
status: STALE
stale_reason: [human-readable description of what changed]
stale_since: [ISO 8601 date]
parent_change_ref: [spec_id and version of changed parent]
```

---

### DEPRECATED

**Meaning:** The component or feature this spec governs has been intentionally removed from the system. The spec is preserved for historical reference only.

**Who can work from it:** No one. DEPRECATED specs are read-only archives.

**AI agent access:** Completely blocked. If an agent encounters a reference to a DEPRECATED spec, it must alert the requester and halt generation.

**How a spec becomes DEPRECATED:**
- Explicit decision: Design Lead or PO formally removes a component from the system
- Never automatic: deprecation requires a deliberate, documented decision

**What happens to specs that reference a DEPRECATED spec:**
- Any L2 referencing a DEPRECATED L1 immediately becomes STALE
- Any L3 referencing a DEPRECATED L2 immediately becomes STALE
- Cascade notifications must be sent to all affected spec owners

**Metadata requirements:**
```yaml
status: DEPRECATED
deprecated_on: [ISO 8601 date]
deprecated_by: [handle]
deprecation_reason: [why this component was removed]
replacement_spec: [spec_id of replacement, if any, else null]
```

---

## Cascade Rules

| Parent changes | Child effect |
|----------------|-------------|
| L1 version increment (minor or major) | All L2s with `inherits_from.spec_id = this L1` → STALE |
| L2 version increment (minor or major) | All L3s with this L2 in `components_used` → STALE |
| L1 DEPRECATED | All L2s referencing it → STALE immediately |
| L2 DEPRECATED | All L3s referencing it → STALE immediately |

Patch version increments (`x.x.1`) on L1 do not cascade — they are corrections without behavioral impact.

---

## Enforcement

### For AI agents

Before generating from any spec, an agent must:

1. Resolve the spec's `status` field
2. If `status ≠ ACTIVE`: halt generation, return the status and the `stale_reason` or `deprecation_reason` to the requester
3. Resolve the full inheritance chain and verify all ancestors are also ACTIVE
4. If any ancestor is not ACTIVE: halt generation, report which ancestor and why

### For teams

The spec health system is only as strong as the tooling that enforces it. Recommended enforcement:

- CI check on `specs/active/` branch: no file may be merged with `status: DRAFT` or `status: STALE`
- Scheduled automation: weekly scan for specs past `next_review` date → auto-set to STALE
- Review notifications: when a spec transitions, all downstream spec owners receive a notification
- PR template: any spec update requires a `spec_id`, `version_bump`, and `impact_statement`

---

## Versioning Within States

Specs use semantic versioning regardless of state:

| Change type | Version bump | Example |
|-------------|-------------|---------|
| Breaking schema change | MAJOR | 1.2.0 → 2.0.0 |
| New section, new variant, new rule | MINOR | 1.2.0 → 1.3.0 |
| Correction, clarification, wording | PATCH | 1.2.0 → 1.2.1 |

A spec moving ACTIVE → DRAFT (for significant revision) increments at minimum MINOR. The new version is not considered ACTIVE until re-reviewed.

---

*Previous: [04-dual-track-model.md](04-dual-track-model.md) · Next: [06-review-matrix.md](06-review-matrix.md)*
