# Changelog

All notable changes to the SDDn framework will be documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
SDDn uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html) with this intent:

- **MAJOR** — breaking changes to the .dsmd schema (existing specs must be migrated)
- **MINOR** — new sections, new framework concepts, backward-compatible additions
- **PATCH** — corrections, clarifications, rewording without schema impact

---

## [0.1.0] — 2026-05-27

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
