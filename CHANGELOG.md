# Changelog

All notable changes to the SDDn framework will be documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
SDDn uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html) with this intent:

- **MAJOR** — breaking changes to the .dsmd schema (existing specs must be migrated)
- **MINOR** — new sections, new framework concepts, backward-compatible additions
- **PATCH** — corrections, clarifications, rewording without schema impact

---

## [0.2.0] - 2026-05-29

### Added

- STALE-ADVISORY state in Spec Health System (5-state model: DRAFT / ACTIVE / STALE-ADVISORY / STALE / DEPRECATED)
- SemVer semantics for L1 changes: patch / minor / major classification checklist (4 questions)
- Cascade Radar with typed message formats: `[STALE-ADVISORY]` vs `[STALE]`
- Mandatory AI warning message for STALE-ADVISORY specs (required in all agent output)
- `stale_advisory_since`, `stale_advisory_deadline`, `cascade_reason`, `parent_change_type` metadata fields in all .dsmd templates
- Minimum Viable Spec (MVS) Checklist - 5 objective criteria for Review Gate passage
- Discovery timebox table with per-spec-type durations (Timebox 1 and reduced Timebox 2)
- Maximum 2 async iterations rule in Track A before mandatory live session escalation
- Live session protocol: reviewer documents, author validates, output goes directly to ACTIVE
- ACTIVE with observations: specs that pass 5/5 MVS but have known gaps are promoted, gaps become `spec-observation` GitHub Issues
- L1 Foundation "sufficient" definition for Track A exit (minimum viable L1 for L2 work to begin)
- Cascade events section in lifecycle document (patch / minor / major effects on GENERATE stage)
- Universal Base Rule in AI Rules Starter Pack (implicit token whitelist, takes precedence over all other rules)
- `framework/08-cascade-radar.md` - cascade classification, message formats, and Design Lead responsibilities
- `CONTRIBUTING.md` - L1 versioning workflow, spec-observation issue format, PR process, RFC model

### Changed

- `framework/05-spec-health-system.md`: 4-state model replaced with 5-state model; cascade rules table updated to distinguish patch/minor/major
- `framework/04-dual-track-model.md`: closed-loop model with Review Gates, timeboxes, MVS checklist, and live session escalation path
- `framework/07-lifecycle.md`: GENERATE stage updated with cascade interruption logic; Cascade Events section added
- `templates/foundation-spec.dsmd`: added stale advisory and cascade metadata fields
- `templates/behavior-spec.dsmd`: added stale advisory and cascade metadata fields
- `templates/delivery-spec.dsmd`: added stale advisory and cascade metadata fields
- `ai-rules-starter-pack/README.md`: Universal Base Rule section added

### Fixed

- Dual-Track Model now has a hard stop preventing infinite discovery cycles (max 2 async iterations + live session)
- Cascade Radar now distinguishes additive changes (STALE-ADVISORY) from breaking changes (STALE), preventing unnecessary generation blocks
- Review Gate now uses an objective 5-point checklist instead of subjective completeness criteria

---

## [0.1.0] - 2026-05-27

### Added

**Framework core**
- L1 Foundation Spec definition, schema, and 7-section structure
- L2 Behavior Spec definition, schema, and 9-section structure
- L3 Delivery Spec definition, schema, and 9-section structure
- AI Generation Rules anatomy mandatory across all layers (Generate / Validate / Forbidden / Prompt Seed)
- Spec Health System with 4 states: DRAFT → ACTIVE → STALE → DEPRECATED
- Dual-Track Model: Track A (Discovery) and Track B (Execution)
- Review Matrix with role assignments and SLA per layer
- Full SDDn lifecycle: DISCOVER → SPEC → REVIEW → GENERATE → VALIDATE → SHIP → EVOLVE

**Principles**
- Principle 1: Spec before pixels
- Principle 2: Intent over aesthetics
- Principle 3: Inheritance over repetition
- Principle 4: AI as executor, human as author
- Principle 5: Living specs

**Templates**
- `foundation-spec.dsmd` — L1 template
- `behavior-spec.dsmd` — L2 template
- `delivery-spec.dsmd` — L3 template

**AI Rules Starter Pack**
- Atomic: button, input, badge
- Molecular: card, form, modal
- Organism: header, sidebar, data-table
- Template: landing-page, dashboard, onboarding-flow

**Examples**
- `design-system-bootstrap/` — complete L1 example (SaaS context)
- `component-primary-button/` — complete L2 example
- `checkout-flow/` — complete L3 example

**Docs**
- `getting-started.md`
- `for-designers.md`
- `for-product-owners.md`
- `for-developers.md`

### Notes

This is the founding release. The .dsmd schema is considered stable for community feedback but not yet locked. No migration guarantees exist until v1.0.0.

---

## [Unreleased]

- Tooling CLI for spec validation
- VS Code extension for .dsmd syntax highlighting
- GitHub Action for spec health enforcement
- v0.2.0 schema refinements based on RFC feedback
