# Dashboard — AI Generation Rules

> Copy section 7 below into your Dashboard L3 Delivery Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Dashboard L3 spec may produce:

- Dashboard layout shell: sidebar (uses Sidebar L2), main content area with top subheader, scrollable content region
- Metric / KPI cards: stat + label + trend indicator pattern (using Card L2 as container)
- Data visualization placeholders: chart containers with declared dimensions and loading skeleton — agent does not generate chart internals unless a chart component L2 spec exists
- Data table sections: reference Data Table L2 spec for table generation
- Page-level loading state: skeleton screens that mirror the dashboard layout structure
- Page-level error state: error message with retry action, matching the system's error state pattern
- Empty state for each data section independently: some sections may have data while others are empty
- Greeting / contextual header: personalized header with user name, date, summary (if declared in spec)
- Quick action area: 3–5 primary workflow shortcuts (using Button L2 components)
- Responsive layout: sidebar collapses on smaller screens, grid collapses to single column
- ARIA: `<main>` for the content area, `<h1>` for the page title, each section wrapped in `<section aria-label="[section name]">`

### Validate

Before approving any generated Dashboard output, verify:

- [ ] One `<h1>` on the page (page/view title)
- [ ] Each dashboard section uses `<section aria-label>` — screen reader users can navigate by landmark
- [ ] Metric cards use the Card L2 spec — no custom card-like `<div>` structures
- [ ] Chart container dimensions use declared tokens or grid-fraction values — no hardcoded pixel heights
- [ ] Loading skeleton uses the system's shimmer animation (motion tokens) and mirrors actual layout
- [ ] Empty state is distinct for each section — not a single "no data" message for the entire dashboard
- [ ] All interactive elements (filters, date pickers, quick actions) reference their respective ACTIVE L2 specs
- [ ] Data that is loading is not represented as "0" or empty — loading state is visually distinct from zero-state
- [ ] Main content area does not use `position: fixed` or `overflow: hidden` at a level that traps keyboard scroll
- [ ] Color-coded metrics (red for bad, green for good) always include a non-color label or icon (color blindness)

### Forbidden

An AI agent working on Dashboard must never:

- Generate charts, graphs, or data visualizations from data without a chart component L2 spec — generate the container and placeholder only
- Use inline `style` attributes for layout or token-equivalent values
- Generate a dashboard with a single loading state for the entire page — each section must have an independent loading state (section-level data independence)
- Apply metric value colors directly from `color-semantic-error` or `color-semantic-success` without also including a non-color indicator (icon or label) — never rely on color alone
- Generate real user data in examples (PII risk) — use clearly fictional data (e.g., "Acme Corp", not real company names)
- Create custom grid systems with hardcoded column widths — use the system's declared grid tokens or CSS grid with fr units
- Add auto-refresh polling behavior without explicit spec declaration (auto-refresh has significant UX and performance implications)

### Prompt Seed

```
Generate a Dashboard view for [Product Name] — [Dashboard Name].

Reference: SDDn Delivery Spec [DASH-L3-NNN] v[version].

Layout:
- Sidebar: [SBR-L2-NNN] v[version], active item: "[current nav item]"
- Main area: top bar with page title "[title]" and [action buttons], scrollable content grid

Content sections (in layout order):
1. KPI cards row — metrics: [list metric names and units]
2. [Chart section name] — [chart type] — container only, [W × H] dimensions
3. [Table section name] — [TBL-L2-NNN] v[version]
4. [Additional section if applicable]

States to generate:
- Loading: skeleton for all sections
- Empty: per-section empty states
- Error: page-level error with retry

Framework: [React / Vue — specify]
Data: use fictional placeholder data

Token constraints:
- Page background: color-surface-base
- Section card: color-surface-raised, shadow-md, radius-lg
- Section gap: spacing-8
- Section heading: typography-heading-md, color-text-primary
- Grid: [N]-column above [breakpoint], 1-column below
- Metric value: typography-display-lg or typography-heading-xl

Component references (all must be ACTIVE):
- Cards: [CARD-L2-NNN] v[version]
- Buttons: [BTN-L2-NNN] v[version]
- Table: [TBL-L2-NNN] v[version]

Accessibility:
- One <h1> (page title), sections as <section aria-label>
- Loading skeleton: aria-hidden="true" on shimmer elements
- Trend indicators: icon + color + aria-label (not color alone)

Do not generate: chart internals, real user data, single-page loading state, auto-refresh logic.
```
