# Data Table — AI Generation Rules

> Copy section 7 below into your Data Table L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Data Table L2 spec may produce:

- Table component with `<table>`, `<thead>`, `<tbody>`, `<tfoot>` semantic structure
- Column header cells: sortable (with sort direction indicator icon) and non-sortable variants
- Row states: default, hover, selected (checkbox or row-click), focused, loading (skeleton rows)
- Bulk selection: header checkbox (selects all / indeterminate state), row-level checkboxes
- Inline row actions: icon button group (edit, delete, view) aligned to rightmost column, appearing on hover or always-visible per spec
- Pagination: page navigation with Previous / Next and page number display; rows-per-page selector
- Filtering: text filter input above table, column-level filter triggers (if in scope)
- Empty state: illustrated or text-only empty state when no rows exist
- Loading skeleton: shimmer rows that match the column structure
- Responsive: horizontal scroll on overflow (no collapsed/stacked row pattern unless explicitly in scope)
- Column resize handles if declared in spec
- ARIA: `role="table"` is implicit on `<table>`; sortable headers use `aria-sort` attribute; selected rows use `aria-selected`; row checkboxes have `aria-label` with row identifier

### Validate

Before approving any generated Data Table output, verify:

- [ ] `<table>`, `<thead>`, `<tbody>` semantic elements are used — not `<div>` grid layout (unless spec explicitly permits grid for performance reasons)
- [ ] `<th>` elements have `scope="col"` (column headers) or `scope="row"` (row headers) attribute
- [ ] Sortable columns: `aria-sort="none"` by default, `aria-sort="ascending"` or `"descending"` when sorted
- [ ] Selected rows: `aria-selected="true"` on selected `<tr>` elements
- [ ] Header checkbox: `aria-checked="mixed"` for indeterminate state (not `indeterminate` — that is a JS property, not an ARIA attribute)
- [ ] Row action buttons are `<button>` elements with `aria-label` (e.g., "Edit [row identifier]")
- [ ] Pagination: previous/next buttons use `aria-disabled` when at bounds; page numbers have `aria-current="page"` on the active page
- [ ] Loading skeleton rows have `aria-hidden="true"` — they are decorative
- [ ] Horizontal overflow container is keyboard-scrollable (`tabindex="0"` on the scroll container)
- [ ] Row hover background uses a declared hover token — no raw `rgba` with arbitrary opacity
- [ ] Font in table cells uses `typography-body-sm` or the spec's declared cell typography token

### Forbidden

An AI agent working on Data Table must never:

- Render a table using CSS grid or flexbox `<div>` layout unless the spec explicitly justifies it (performance requirement with stated row count threshold)
- Generate a `<table>` without `<th scope="col">` headers — all data tables must have column or row headers for screen reader navigation
- Generate horizontal scroll with `overflow-x: auto` on the `<table>` element directly — wrap it in a scroll container and give the container `tabindex="0"` for keyboard access
- Implement row selection by changing row background color alone — use `aria-selected` AND a visual indicator (checkbox or left border)
- Generate bulk-action patterns (delete selected, export selected) without first checking that bulk actions are declared in the spec's scope
- Add column-level sorting to columns declared as non-sortable in the spec
- Generate virtual scrolling / windowed rows without explicit spec permission (this changes the accessibility and keyboard navigation contract significantly)
- Use `title` attributes on truncated cell content as the only tooltip mechanism — use a proper tooltip component

### Prompt Seed

```
Generate a Data Table component for [System Name].

Reference: SDDn Behavior Spec [TBL-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Columns: [list column names, data types, and whether each is sortable]
Features in scope: sorting, row selection (checkbox), inline row actions (edit, delete), pagination
Features out of scope: [column filtering / column resize / virtual scroll — adjust per spec]
Framework: [React / Vue / HTML — specify]

Token constraints:
- Header background: color-surface-raised
- Header text: typography-label-sm, color-text-secondary
- Row background (default): color-surface-base
- Row background (hover): [hover token]
- Row background (selected): [selected token — typically color-action-primary at low opacity]
- Row border: color-border-default
- Cell text: typography-body-sm, color-text-primary
- Cell padding: spacing-2 (vertical) × spacing-4 (horizontal)
- Action icon size: [spacing token]
- Pagination: [reference paginator spec or inline]

Accessibility:
- <table>, <thead>, <tbody> semantic structure
- <th scope="col"> on all column headers
- aria-sort on sortable headers
- aria-selected="true" on selected rows
- Checkbox indeterminate: aria-checked="mixed"
- Row action buttons: aria-label="[Action] [row identifier]"
- Scroll container: tabindex="0" for keyboard scroll

Do not generate: div-grid tables, color-only row selection, non-declared bulk actions.
```
