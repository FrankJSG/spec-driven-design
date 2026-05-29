# Spec Health System

A spec's state is not metadata - it is a workflow control. The health system ensures that agents and developers always know whether a spec is trustworthy enough to build from.

---

## The Five States

```
  ┌─────────┐   approved     ┌────────┐   minor parent    ┌────────────────┐
  │  DRAFT  │──────────────▶ │ ACTIVE │──────change──────▶ │ STALE-ADVISORY │
  └─────────┘                └───┬────┘                    └───────┬────────┘
       ▲                         │                                 │
       │ revision                │ major                           │ timebox
       └─────────────────────────┤ parent                          │ expires
                                 │ change                          ▼
                                 │                          ┌────────────┐
                                 └─────────────────────────▶│   STALE    │
                                                            └─────┬──────┘
                                       re-approved (both)         │
                              ACTIVE ◀─────────────────────┴──    │ component
                                                                   │ removed
                                                            ┌──────▼─────┐
                                                            │ DEPRECATED │
                                                            └────────────┘
```

---

## State Summary

| State | AI can operate | Condition |
|-------|---------------|-----------|
| DRAFT | No | Under construction |
| ACTIVE | Yes | Approved and synchronized |
| STALE-ADVISORY | Yes + mandatory warning | Minor change in parent, within timebox |
| STALE | No | Major change, or timebox expired |
| DEPRECATED | No | Removed from system |

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
version: [semver - can be 0.x.x]
owner: [spec author handle]
review_requested: [ISO 8601 date or null]
```

---

### ACTIVE

**Meaning:** The spec has been reviewed, approved, and is the authoritative definition for this element. Builds may proceed.

**Who can work from it:** Designers, developers, AI agents, and QA. This is the production-grade contract.

**AI agent access:** Full - agents may generate output from ACTIVE specs, constrained by the spec's AI Generation Rules.

**Transitions out:**
- → STALE-ADVISORY: A parent spec (L1 or L2) increments with a minor version change
- → STALE: A parent spec (L1 or L2) increments with a major version change
- → STALE: The `next_review` date passes without re-review
- → STALE: A manual reviewer trigger marks the spec for re-evaluation
- → DEPRECATED: The component or feature this spec governs is intentionally removed
- → DRAFT: A significant change is required; spec enters revision cycle

**Metadata requirements:**
```yaml
status: ACTIVE
version: [semver - 1.x.x or higher for first ACTIVE state]
owner: [spec owner handle]
last_reviewed: [ISO 8601 date]
reviewed_by: [reviewer handle(s)]
next_review: [ISO 8601 date - typically 90 days from last_reviewed]
stale_advisory_since: null
stale_advisory_deadline: null
cascade_reason: null
parent_change_type: null
```

---

### STALE-ADVISORY

**Meaning:** The spec was valid and a parent spec has changed with a minor version increment. The change is additive and does not break compatibility, but the spec owner must verify whether the change applies. The spec remains operational during a fixed timebox.

**Who can work from it:** Designers, developers, and AI agents may continue to work from a STALE-ADVISORY spec. All AI-generated output must carry the mandatory warning message (see below).

**AI agent access:** Allowed with mandatory warning. The agent must prepend the warning message to all output before delivering results.

**Timebox:**
- L2 Behavior Specs: 5 business days from `stale_advisory_since`
- L3 Delivery Specs: 3 business days from `stale_advisory_since`

**What triggers STALE-ADVISORY:**
- A parent L1 spec releases a minor version increment (e.g., v1.2.0 → v1.3.0)
- A parent L2 spec releases a minor version increment affecting an L3 that references it

**Transitions out:**
- → ACTIVE: Spec owner reviews the parent change, updates `inherits_from.last_verified` if no impact (or updates affected sections if there is impact), resubmits for review, reviewer approves
- → STALE: Timebox expires without owner action

**Mandatory AI warning message:**

```
WARNING: SPEC STALE-ADVISORY
This spec operates on a version with pending validation changes.
Parent: [parent-spec] v[x.x] → v[x.y] (minor)
Review deadline: [stale_advisory_deadline]
Generated output may not reflect the latest system state.
Full validation recommended before using in production.
```

This warning must appear at the top of all agent output when the spec is STALE-ADVISORY. It cannot be omitted or moved to a footnote.

**Cascade message format:**

```
[STALE-ADVISORY] [spec-name].dsmd
Reason: minor change in [parent-spec] v[x.x] → v[x.y]
Added/changed: [description]
Estimated impact: low - additive change, does not break compatibility
Deadline: [date] ([n] business days)
Action required: verify if change applies to this component
```

**Metadata requirements:**
```yaml
status: STALE-ADVISORY
stale_advisory_since: [ISO 8601 date]
stale_advisory_deadline: [ISO 8601 date]
cascade_reason: [description of what changed in the parent spec]
parent_change_type: minor
```

---

### STALE

**Meaning:** The spec was valid but a dependency has changed with a breaking or significant update, or the advisory timebox has expired without action. It may no longer accurately describe how the element should behave.

**Who can work from it:** No one builds from a STALE spec without explicit risk acknowledgement. Ongoing in-flight work should pause pending re-review.

**AI agent access:** Blocked - agents must refuse to generate from STALE specs and surface the stale status and cascade message to the requester.

**How a spec becomes STALE:**
- Major parent change: the L1 spec referenced by an L2 releases a major version increment
- Major parent change: an L2 spec referenced by an L3 releases a major version increment
- Timebox expiry: a STALE-ADVISORY spec's timebox passes without owner action
- Manual trigger: a reviewer marks a spec as requiring re-evaluation
- Scheduled trigger: `next_review` date passes without re-review

**Transitions out:**
- → ACTIVE: Spec owner reviews the changed parent, updates the spec, resubmits, reviewer approves
- → DEPRECATED: On re-review, the element is determined to be removed

**The re-review process for STALE:**
1. Spec owner receives cascade notification
2. Owner reviews the parent spec changes and assesses impact
3. If no impact: update `inherits_from.last_verified` and `last_reviewed`, resubmit for review
4. If impact: update affected sections, increment version, resubmit for review
5. Reviewer approves → ACTIVE

**Cascade message format:**

```
[STALE] [spec-name].dsmd
Reason: major change in [parent-spec] v[x.x] → v[x.y]
Breaking change: [description]
Impact: high - [token/rule] used in [n] sections of this spec
Action required: mandatory update before resuming AI generation
```

**Metadata requirements:**
```yaml
status: STALE
stale_reason: [human-readable description of what changed]
stale_since: [ISO 8601 date]
parent_change_ref: [spec_id and version of changed parent]
cascade_reason: [description]
parent_change_type: major
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

| Parent change type | Effect on L2/L3 specs | AI operation |
|-------------------|----------------------|--------------|
| L1 patch (x.x.N) | No cascade | Unaffected |
| L1 minor (x.N.0) | Inheriting L2s → STALE-ADVISORY + timebox | Allowed with warning |
| L1 major (N.0.0) | Inheriting L2s → STALE immediately | Blocked |
| L2 minor (x.N.0) | Referencing L3s → STALE-ADVISORY + timebox | Allowed with warning |
| L2 major (N.0.0) | Referencing L3s → STALE immediately | Blocked |
| L1 DEPRECATED | All inheriting L2s → STALE immediately | Blocked |
| L2 DEPRECATED | All referencing L3s → STALE immediately | Blocked |

---

## Enforcement

### For AI agents

Before generating from any spec, an agent must:

1. Resolve the spec's `status` field
2. If `status` is DRAFT: halt, return "Spec is DRAFT - not approved for generation"
3. If `status` is STALE-ADVISORY: continue, but prepend the mandatory warning message to all output
4. If `status` is STALE: halt, return the STALE cascade message
5. If `status` is DEPRECATED: halt, return the deprecation reason and `replacement_spec` if present
6. If `status` is ACTIVE: resolve the full inheritance chain and verify all ancestors
7. If any ancestor is not ACTIVE or STALE-ADVISORY: halt, report which ancestor and its status

When operating on a STALE-ADVISORY spec, the agent must:
- Prepend the warning message verbatim before any generated output
- Note the `stale_advisory_deadline` so the user understands the time sensitivity
- Not suppress or abbreviate the warning even if the user requests shorter output

### For teams

- CI check on `specs/active/` branch: no file may be merged with `status: DRAFT` or `status: STALE`
- Scheduled automation: daily scan for specs past `stale_advisory_deadline` → auto-set to STALE
- Scheduled automation: weekly scan for specs past `next_review` date → auto-set to STALE
- Cascade notifications: when a parent spec releases a new version, all downstream spec owners receive a typed cascade message (format above)
- PR template: any spec update requires a `spec_id`, `version_bump`, `parent_change_type`, and `impact_statement`

---

## Versioning Within States

Specs use semantic versioning regardless of state:

| Change type | Version bump | Example |
|-------------|-------------|---------|
| Breaking schema change | MAJOR | 1.2.0 → 2.0.0 |
| New section, new variant, new rule | MINOR | 1.2.0 → 1.3.0 |
| Correction, clarification, wording | PATCH | 1.2.0 → 1.2.1 |

A spec moving ACTIVE → DRAFT (for significant revision) increments at minimum MINOR. The new version is not considered ACTIVE until re-reviewed.

For guidance on classifying L1 version changes as patch, minor, or major, see [`framework/08-cascade-radar.md`](08-cascade-radar.md).

---

*Previous: [04-dual-track-model.md](04-dual-track-model.md) · Next: [06-review-matrix.md](06-review-matrix.md)*
