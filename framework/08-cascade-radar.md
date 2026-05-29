# Cascade Radar

Every time an L1 Foundation Spec releases a new version, all L2 and L3 specs that inherit from it face a choice: continue operating as-is, continue with a warning, or pause until updated. The Cascade Radar is the system that makes that decision consistently.

The Design Lead classifies every L1 version change before publishing it. That classification determines the cascade type, which determines the effect on all inheriting specs.

---

## L1 Version Classification Checklist

Apply these four questions in order to every L1 change before assigning a version increment. The questions are ordered from most breaking to least. Stop at the first "yes."

**Question 1 - Does this change eliminate, rename, or modify the value of an existing token?**

"Modify the value" includes: changing a color value, changing a spacing unit, changing a typography size or weight. "Rename" means the CSS custom property name changes. "Eliminate" means a token that existed in the previous version is no longer present.

If yes: classify as **major**.

---

**Question 2 - Does it modify the scope or semantics of an existing design principle - not just its wording?**

A semantic change means the principle now governs a different set of decisions. Example: changing "Clarity over density: data dashboards should reduce cognitive load" to "Clarity over density: all surfaces should reduce cognitive load" expands the scope of the principle beyond dashboards. Wording change: fixing a typo or rewriting for clarity without changing what the principle governs.

If yes: classify as **major**.

---

**Question 3 - Does it add a new token, principle, or rule without touching existing ones?**

"Without touching existing ones" means all tokens, values, and rules present in the previous version remain present and unchanged. The only change is an addition.

If yes: classify as **minor**.

---

**Question 4 - Does it fix wording, spelling, or metadata without altering semantics?**

Metadata includes: `last_reviewed`, `reviewed_by`, `next_review`, `owner`, `contributors`. Wording includes: reformatting a table, fixing a typo, clarifying an intent statement without changing its meaning.

If yes: classify as **patch**.

---

**If none of the above apply:** the change does not fit a clean classification. Discuss with the full review team before publishing. A change that cannot be classified is likely either a major or requires splitting into two separate releases.

The canonical home for this checklist in the workflow is [`CONTRIBUTING.md`](../CONTRIBUTING.md). Every L1 PR must include a `version_classification` field with the result of applying these questions.

---

## Cascade Behavior by Change Type

| Change type | Version pattern | Effect on inheriting specs | AI operation on affected specs |
|-------------|----------------|---------------------------|-------------------------------|
| patch | x.x.N | No cascade | Unaffected |
| minor | x.N.0 | STALE-ADVISORY + timebox | Allowed with mandatory warning |
| major | N.0.0 | STALE immediately | Blocked |
| DEPRECATED | - | STALE immediately | Blocked |

**Timebox by spec type (minor cascade):**
- L2 specs: 5 business days from cascade notification
- L3 specs: 3 business days from cascade notification

If the spec owner does not act within the timebox, the spec auto-degrades from STALE-ADVISORY to STALE. No further warning is issued.

---

## Cascade Message Formats

Two typed message formats are used across the framework. These formats are required in cascade notifications, in agent output headers when operating on affected specs, and in CI tooling reports.

### STALE-ADVISORY message

Sent when: a minor parent change cascades to an inheriting spec.

```
[STALE-ADVISORY] [spec-name].dsmd
Reason: minor change in [parent-spec] v[x.x] → v[x.y]
Added/changed: [description of what was added or changed in the parent]
Estimated impact: low - additive change, does not break compatibility
Deadline: [ISO 8601 date] ([n] business days)
Action required: verify if change applies to this component
```

**Fields:**
- `[spec-name]`: the filename of the affected spec (e.g., `orbit-button.dsmd`)
- `[parent-spec]`: the spec_id of the changed parent (e.g., `ODS-L1-001`)
- `v[x.x] → v[x.y]`: the version before and after the parent change
- `[description]`: a one-line plain-language description of what changed in the parent
- `[ISO 8601 date]`: the deadline date computed from the notification date + timebox
- `[n]`: the number of business days in the timebox for this spec type

### STALE message

Sent when: a major parent change cascades to an inheriting spec, or when a STALE-ADVISORY timebox expires.

```
[STALE] [spec-name].dsmd
Reason: major change in [parent-spec] v[x.x] → v[x.y]
Breaking change: [description of what was broken - token renamed, value changed, etc.]
Impact: high - [token/rule] used in [n] sections of this spec
Action required: mandatory update before resuming AI generation
```

**Fields:**
- `[description]`: identifies the specific token or rule that changed, and how
- `[token/rule]`: names the exact element from the parent spec that changed (e.g., `color-action-primary`, `Design Principle 2`)
- `[n]`: the number of sections in the affected spec that reference the broken element

When STALE is triggered by timebox expiry (not a major change), the Reason line reads:

```
Reason: STALE-ADVISORY timebox expired without owner action
```

---

## Reading the Cascade Signal

When you receive a STALE-ADVISORY message for one of your specs, the action is always the same:

1. Open the parent spec and read the changelog entry for the minor version
2. Check whether any added tokens, principles, or rules apply to your spec
3. If they apply: update your spec's relevant sections, increment your spec version (MINOR), resubmit for review
4. If they do not apply: update `inherits_from.last_verified` to today's date, resubmit for review (no section changes needed, PATCH version bump)
5. Reviewer confirms and approves → spec returns to ACTIVE

The STALE-ADVISORY state is designed so that in most cases (additive minor changes with no impact on your component), the resolution is a single-line metadata update and a PATCH review. The timebox exists to prevent permanent drift between parent and child specs, not to create work.

When you receive a STALE message, the action requires reading the full breaking change description and doing a real assessment. A STALE spec cannot be re-approved with a metadata-only change. The sections that reference the broken element must be updated to reflect the new parent state.

---

## Who Runs the Cascade Radar

The cascade signal originates from the Design Lead when publishing a new L1 version. The Design Lead is responsible for:

1. Applying the classification checklist to every L1 change before release
2. Including `version_classification: [patch|minor|major]` in the L1 PR description
3. Triggering the cascade notifications to all spec owners immediately after the L1 is merged to `specs/active`

If tooling is in place, cascade notifications are sent automatically when a new L1 version is detected. Without tooling, the Design Lead sends cascade messages manually to all affected spec owners on the day of the L1 release.

See [`CONTRIBUTING.md`](../CONTRIBUTING.md) for the step-by-step workflow for L1 maintainers.

---

*Previous: [07-lifecycle.md](07-lifecycle.md) · Back to [README](../README.md)*
