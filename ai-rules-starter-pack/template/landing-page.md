# Landing Page — AI Generation Rules

> Copy section 7 below into your Landing Page L3 Delivery Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Landing Page L3 spec may produce:

- Complete page layout using the declared section structure: hero, social proof, features/benefits, secondary CTA, footer
- Hero section: headline, subheadline, primary CTA (references Button L2), optional media (image, video, or animated illustration)
- Feature / benefit sections: icon + headline + description pattern, in grid or alternating two-column layout
- Social proof section: logo grid (partner/customer logos) or testimonial cards (using Card L2)
- Secondary CTA section: full-width background, headline, body, CTA button
- Navigation header: use the system's Header L2 spec
- Responsive layout: all sections adapt per the system's breakpoint tokens
- Semantic HTML structure: `<main>`, `<section>` with descriptive `aria-label`, `<h1>` for page title (one per page), `<h2>` for section headings
- Open Graph meta tags in `<head>` for social sharing
- Structured data (JSON-LD) for the organization if in scope
- Performance: above-the-fold images as `loading="eager"`, below-the-fold as `loading="lazy"`

### Validate

Before approving any generated Landing Page output, verify:

- [ ] One and only one `<h1>` on the page — the hero headline
- [ ] Section headings use `<h2>` — no skipped heading levels
- [ ] All CTA buttons reference the Button L2 spec (not custom styled `<a>` tags styled as buttons)
- [ ] Images have meaningful `alt` text — not empty, not "image", not the filename
- [ ] Decorative images have `alt=""`
- [ ] Color contrast of hero text against hero background passes WCAG 2.2 AA (especially when text is overlaid on an image or gradient)
- [ ] Primary CTA appears above the fold on desktop and mobile
- [ ] Page uses `color-surface-base` as the root background — section alternation uses declared surface tokens, not custom colors
- [ ] All spacing between sections uses declared spacing tokens — no raw `margin: 80px` values
- [ ] Responsive behavior: all elements are usable and readable at the system's declared mobile breakpoint
- [ ] No autoplaying video with sound — if video is used, `muted autoplay` is the maximum; controls must be available

### Forbidden

An AI agent working on Landing Page must never:

- Generate more than one `<h1>` element
- Use `<button>` elements for navigation links (CTAs that navigate to another page must be `<a>` elements, styled as buttons)
- Generate hero images without `alt` text (unless the image is decorative — then `alt=""` is required, not omitted)
- Add cookie consent banners, pop-ups, or exit-intent overlays — these are product-level decisions requiring their own specs
- Generate custom color values for section backgrounds (alternating sections must use declared surface tokens)
- Create more than two distinct CTA actions in the hero (one primary, one optional secondary — no tertiary)
- Generate text that overlays images without ensuring the contrast ratio is validated (image backgrounds are dynamic; the agent should flag this for human review)
- Inline every image as base64 — use external references

### Prompt Seed

```
Generate a landing page for [Product/Feature Name] for [System Name].

Reference: SDDn Delivery Spec [LP-L3-NNN] v[version].

Section structure (in order):
1. Header (nav) — [HDR-L2-NNN] v[version]
2. Hero — headline: "[headline]", subheadline: "[subheadline]", primary CTA: "[CTA label]" → [destination]
3. Social proof — [logo grid / testimonials — specify]
4. Features — [N] features: [list them]
5. Secondary CTA — headline: "[headline]", CTA: "[label]"
6. Footer — [minimal / full — specify]

Framework: [React / Next.js / HTML — specify]

Token constraints:
- Page background: color-surface-base
- Section alt background: color-surface-raised
- Section vertical padding: spacing-16
- Feature grid gap: spacing-8
- Max content width: [value from L1 layout tokens]
- Hero headline: typography-display-lg
- Section heading: typography-heading-xl
- Body: typography-body-lg

Component references:
- Buttons: [BTN-L2-NNN] v[version]
- Testimonial cards: [CARD-L2-NNN] v[version]

Performance:
- Hero image: loading="eager"
- Below-fold images: loading="lazy"
- Heading level hierarchy: one <h1>, sections use <h2>

Do not generate: multiple <h1>s, button-as-nav-link, autoplay video with sound, custom section background colors.
```
