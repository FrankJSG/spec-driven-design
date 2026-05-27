# Card — AI Generation Rules

> Copy section 7 below into your Card L2 Behavior Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Card L2 spec may produce:

- Card component in all declared variants: default (flat), raised (with shadow), outlined (border only), interactive (clickable entire surface)
- Internal slot structure: header (optional), media (optional), body (required), footer (optional)
- State variants for interactive card: default, hover, focus, active, disabled
- Loading skeleton overlay that mirrors the card's layout structure (shimmer animation using motion tokens)
- Empty state variant for cards that present data that may be absent
- Stacked / list card variant if in spec scope (reduced padding, no radius for items in a list)
- ARIA: interactive cards get `role="article"` for content cards, or appropriate role per content type; clickable card surfaces get `role="button"` with keyboard support if the entire card is interactive
- Responsive layout notes: when should card stack vs. remain in grid (this is layout guidance, not interactive behavior — generate as documentation comment in code)

### Validate

Before approving any generated Card output, verify:

- [ ] Background uses `color-surface-raised` token (not `color-surface-base` — cards sit above the page surface)
- [ ] Shadow on raised variant uses the correct elevation token (`shadow-md` not `shadow-lg`)
- [ ] Border-radius uses `radius-lg` — cards are larger elements, use the larger radius token
- [ ] Internal padding uses the spacing token declared in the spec (typically `spacing-6` or `spacing-8`)
- [ ] Gap between internal elements uses spacing tokens — not arbitrary px values
- [ ] Loading skeleton uses `color-surface-overlay` at reduced opacity and a shimmer animation with `motion-*` tokens
- [ ] Interactive card (clickable surface): entire card has `role="button"` or `<a>` wrapper with keyboard support
- [ ] Interactive card focus state shows focus ring using `color-focus-ring` — visible against card background
- [ ] Media slot does not overflow the card's border-radius (use `overflow: hidden` on card container)
- [ ] No nested interactive elements inside a fully-interactive card surface (link inside clickable card = keyboard trap risk)

### Forbidden

An AI agent working on Card must never:

- Generate drop shadows on outlined card variants (outlined = border only, no shadow)
- Add hover states to non-interactive card variants (a content card is not a button; adding hover styles implies interactivity)
- Generate cards with more than one primary CTA in the footer (one card, one primary action)
- Use `color-surface-base` as card background (cards must visually lift from the page surface)
- Nest a fully interactive card inside another fully interactive card (creates inaccessible interaction layers)
- Generate overflow-visible media that bleeds outside card bounds without explicit spec permission
- Add motion effects beyond `shadow` and `border-color` changes on hover (no translate, scale, or z-index animations unless declared in the spec)

### Prompt Seed

```
Generate a Card component for [System Name].

Reference: SDDn Behavior Spec [CARD-L2-NNN] v[version], inheriting from Foundation Spec [SYSTEM-L1-NNN] v[version].

Variants required: default, raised, outlined, interactive
Slots: header (optional), media (optional), body (required), footer (optional)
States (interactive only): default, hover, focus, active, disabled
Framework: [React / Vue / HTML — specify]

Token constraints:
- Background: color-surface-raised
- Shadow (raised): shadow-md
- Border (outlined): color-border-default
- Radius: radius-lg
- Padding (internal): spacing-6
- Gap (between slots): spacing-4
- Focus ring (interactive): color-focus-ring + shadow-focus
- Transition: motion-duration-fast + motion-ease-standard

Accessibility:
- Content cards: role="article" or semantic HTML (<article>)
- Interactive cards: role="button" or <a> wrapper, Tab focusable, Enter to activate
- No nested interactive elements inside a fully-interactive card surface

Do not generate: hover states on non-interactive variants, shadow on outlined variant, scale/translate hover animations.
```
