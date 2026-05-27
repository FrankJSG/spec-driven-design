# Badge — AI Generation Rules

> Copy section 7 below into your Badge L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Badge L2 spec may produce:

- Badge component in all declared semantic variants: default, success, warning, error, info
- Neutral / status variants if declared: outline, ghost, soft (background tinted, no hard border)
- All declared size variants: sm, md
- Text-only badge, icon-only badge, and icon + text badge (if icon support is in scope)
- Dot indicator variant for notification counts or status pulses (if in scope)
- Count badge variant (for notification counts, cart items): number display, `+99` truncation at threshold
- ARIA annotation: decorative badges get `aria-hidden="true"`; semantic badges get `role="status"` or `aria-label` with the full meaning
- Usage examples: inline in text, attached to avatar/icon, inside table cells, inside list items

### Validate

Before approving any generated Badge output, verify:

- [ ] Semantic variant colors map to the correct L1 semantic tokens: `color-semantic-error`, `color-semantic-success`, `color-semantic-warning`, `color-semantic-info`
- [ ] No semantic color is used for a non-semantic badge (e.g., using error red for "Pro" plan badge)
- [ ] Text color in all variants passes WCAG 2.2 AA contrast against the badge background
- [ ] Font references `typography-label-sm` token — badges never use body or heading tokens
- [ ] Border-radius references `radius-sm` or `radius-full` (pill) — not a raw value
- [ ] Padding references spacing tokens — no raw pixel values
- [ ] Decorative badges have `aria-hidden="true"` — they do not add noise to screen reader flow
- [ ] Semantic status badges have `role="status"` or meaningful `aria-label`
- [ ] Count badge has `aria-label` that expresses the count in words (e.g., "12 notifications")
- [ ] Minimum height meets touch-target requirement if badge is interactive (rare but valid)

### Forbidden

An AI agent working on Badge must never:

- Create a new semantic variant not declared in the spec (no "purple" badge for "premium", no "teal" badge for "new" without a declared variant)
- Use a semantic color (error, success, warning) for a non-semantic purpose (using red for a "featured" badge is a brand decision, not an error indicator — it confuses users and violates intent-over-aesthetics)
- Generate interactive badge behavior (click, hover state change) unless the spec explicitly declares interactive badges in scope
- Display counts above the declared truncation threshold without truncation (e.g., showing "234" when spec says truncate at "99+")
- Generate long strings inside a badge — badge content is 1–3 words or a number; longer content belongs in a tag or label component
- Apply drop shadows or elevation tokens to badges — badges are flat label elements

### Prompt Seed

```
Generate a Badge component for [System Name].

Reference: SDDn Behavior Spec [BADGE-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Variants required: default, success, warning, error, info
Sizes: sm, md
Sub-types: text-only, icon + text, count (with +99 truncation)
Framework: [React / Vue / HTML — specify]

Token constraints:
- Default background: color-surface-raised
- Success background: [color-semantic-success at reduced opacity or full — per spec]
- Warning background: [color-semantic-warning]
- Error background: [color-semantic-error]
- Info background: [color-semantic-info]
- Text: color-text-primary (default), color-text-on-action (colored variants) — verify contrast
- Font: typography-label-sm
- Radius: radius-full (pill) or radius-sm (rounded rect) — per spec
- Padding: spacing-1 (vertical) × spacing-2 (horizontal)

Accessibility:
- Decorative badges: aria-hidden="true"
- Status badges: role="status" or aria-label with full meaning
- Count badges: aria-label="[N] [items]"

Do not generate: interactive states (unless in scope), new semantic color variants, long-text badges.
```
