# Contributing to SDDn

SDDn is an open methodology framework published under the Community RFC model. Contributions take two forms: changes to the framework itself (methodology documents, templates, AI rules) and additions of examples or documentation.

---

## Community RFC Model

SDDn v0.1.x and v0.2.x are in the RFC (Request for Comments) phase. The framework is complete and internally consistent, but no backward-compatibility contract exists until v1.0.0. This means:

- Breaking changes to the .dsmd schema may occur in minor versions before v1.0.0
- Community feedback directly shapes the v1.0.0 specification
- Issues opened as questions or challenges to the framework are valid and welcome

**Before submitting a PR that changes methodology:** open an issue first and label it `rfc`. Describe the problem you've identified and the change you propose. This gives the community a chance to weigh in before the PR is written.

**For additions (examples, new AI rules, docs):** PRs without prior issues are accepted if the addition clearly fits the existing framework without modifying it.

---

## L1 Version Classification - Required for All L1 PRs

Every PR that modifies an L1 Foundation Spec must include a `version_classification` field in the PR description. This classification determines whether the change cascades as a patch, minor, or major to all inheriting specs. See [`framework/08-cascade-radar.md`](framework/08-cascade-radar.md) for the full cascade behavior.

Apply these four questions in order and stop at the first "yes":

**1. Does this change eliminate, rename, or modify the value of an existing token?**
"Modify" includes changing a color hex, spacing multiplier, typography size or weight. "Rename" means the token name (CSS property name) changes. "Eliminate" means removing a token that existed in the previous version.
→ If yes: **major**

**2. Does it modify the scope or semantics of an existing design principle - not just its wording?**
A semantic change expands or contracts what the principle governs. Rewording for clarity without changing the principle's scope is not semantic.
→ If yes: **major**

**3. Does it add a new token, principle, or rule without changing existing ones?**
Every element from the previous version must be present, unchanged, and with the same value.
→ If yes: **minor**

**4. Does it fix wording, spelling, or metadata without altering semantics?**
Metadata: `last_reviewed`, `reviewed_by`, `next_review`, `owner`, `contributors`. Wording: typos, formatting, clarity improvements that preserve meaning.
→ If yes: **patch**

**If none apply:** do not merge. Post the change as an issue and discuss with the review team.

**PR description template for L1 changes:**
```
version_classification: [patch | minor | major]
version_before: [e.g., 1.2.0]
version_after: [e.g., 1.3.0]
tokens_affected: [list token names, or "none"]
cascade_description: [one line describing what changed for spec owners who receive the cascade message]
```

---

## Opening spec-observation Issues

When a spec passes a Review Gate with observations (see [`framework/04-dual-track-model.md`](framework/04-dual-track-model.md)), those observations must be opened as GitHub Issues. When a live session produces undocumented gaps, those gaps also become Issues.

**Label:** `spec-observation`

**Required fields in every spec-observation issue:**
- `spec_id`: the ID of the spec this observation belongs to (e.g., `ODS-BTN-L2-001`)
- `observation`: a specific, actionable description of what is missing or incomplete
- `severity`: `low` (cosmetic gap), `medium` (functional gap without production impact), `high` (gap that may affect generated output)
- `resolution_path`: what the spec owner needs to do to resolve this observation

**Example:**
```
spec_id: ODS-BTN-L2-001
observation: Loading state does not specify timeout behavior. Spec section 5 says "button must not remain in loading state indefinitely" but does not define the timeout duration or what happens when it triggers.
severity: medium
resolution_path: Add a timeout threshold (suggested: 10s) and error transition to section 5 Interaction Contract.
```

Observations are resolved in the next spec version review. They do not block the current ACTIVE status unless the reviewers elevate them during the gate.

---

## PR Process for Spec Updates

### Updating an existing spec (L2 or L3)

1. Create a branch from `main` named `spec/[spec-id]-[brief-description]`
2. Edit the .dsmd file. For non-trivial changes, increment the version:
   - Behavioral change, new state, new rule: MINOR
   - Wording, metadata, clarification: PATCH
3. Update `last_reviewed`, `reviewed_by`, and `next_review` in the YAML frontmatter
4. If STALE or STALE-ADVISORY: clear the stale fields and set `status: DRAFT`
5. Submit PR with the review request format:
   ```
   spec_id: [ID]
   version_before: [x.x.x]
   version_after: [x.x.x]
   change_type: [patch | minor | major]
   summary: [one sentence describing the change]
   reviewer_requested: [handle of designated reviewer per Review Matrix]
   ```
6. Designated reviewer(s) review within the SLA (see [`framework/06-review-matrix.md`](framework/06-review-matrix.md))
7. On approval: merge to `main`, move file to `specs/active/` if using the recommended directory structure

### Updating framework documents

Framework document changes (anything in `framework/`) require the same review as an L1 spec: Design Lead + Product Owner review within 48h.

For significant framework changes (anything that modifies the .dsmd schema or introduces a new state), open an RFC issue first.

### Adding to the AI Rules Starter Pack

1. File must include all four subsections: Generate, Validate, Forbidden, Prompt Seed
2. Validate and Forbidden sections must include the Universal Base Rule reference (see [`ai-rules-starter-pack/README.md`](ai-rules-starter-pack/README.md))
3. Prompt Seed must be functional - test it with at least one LLM before submitting
4. PR may be submitted without a prior issue if the component is not already covered

---

## What Makes a Good Framework Issue

SDDn issues fall into three types:

**RFC (label: `rfc`):** A challenge to or proposed extension of the framework itself. "The 5/5 MVS Checklist is too minimal - a spec could pass 5/5 and still have no edge cases" is a valid RFC. These issues drive the next minor or major framework version.

**spec-observation (label: `spec-observation`):** A documented gap in a specific spec. See the format above.

**bug (label: `bug`):** An internal inconsistency or contradiction in the framework documents. "Section 3 of `05-spec-health-system.md` says timebox is 5 days but the timebox table in `04-dual-track-model.md` says 3 days" is a bug.

**What is not a valid issue:**
- Requests to change the framework to match a specific team's existing workflow (the framework does not adapt to workflows; teams adopt the framework)
- Style preferences about the framework's writing
- Feature requests for tooling (the framework is tooling-agnostic; tooling ideas belong in a separate repository)

---

## Code of Conduct

SDDn is a technical methodology framework. Contributions are expected to be specific, reasoned, and grounded in the framework's principles. The five SDDn principles in [`framework/00-principles.md`](framework/00-principles.md) are also the principles for engaging with the framework's community: spec before pixels (propose before implementing), intent over aesthetics (argue from principle, not from preference).
