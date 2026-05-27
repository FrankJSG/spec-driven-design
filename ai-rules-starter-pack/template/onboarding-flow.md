# Onboarding Flow — AI Generation Rules

> Copy section 7 below into your Onboarding Flow L3 Delivery Spec.
> Replace tokens and spec IDs with your system's values.

---

## Section 7: AI Generation Rules

### Generate

An AI agent operating on an ACTIVE Onboarding Flow L3 spec may produce:

- Multi-step onboarding container: step indicator (progress bar or step dots), content area, action footer (Back / Next / Skip / Finish)
- Each declared step as a distinct screen: welcome, profile setup, team invite, product tour trigger, completion
- Step indicator: current step highlighted, completed steps marked (check icon), future steps visually muted — using token-based states
- Skip step mechanism: "Skip for now" action on optional steps — renders as ghost/link button, positioned below primary action
- Back navigation: returns to previous step without data loss; step state is preserved
- Completion / success screen: celebration moment (animated checkmark or illustration), summary of what was set up, primary CTA to enter the product
- Validation per step: required fields validated before advancing; error states use the system's Input and Form L2 specs
- Mobile layout: single-column, full-viewport step screens; swipe gestures are NOT generated (navigation is button-based only)
- ARIA: step indicator announces current position via `aria-label="Step [N] of [N]: [step name]"`; each step screen has `<h1>` for the step title; live region announces step transitions

### Validate

Before approving any generated Onboarding Flow output, verify:

- [ ] Step indicator correctly shows: completed, current, and upcoming states using declared tokens
- [ ] Each step has exactly one `<h1>` (the step title)
- [ ] Required field validation happens on Next/Finish action — not on page load or on field blur during onboarding (onboarding is forgiving; validate before advance, not before user finishes typing)
- [ ] Skip actions are visually de-emphasized (ghost or link style) — they must not compete with the primary Next action
- [ ] Back button is present on all steps except the first
- [ ] Completion screen has a clear, single CTA to enter the product — no multiple competing actions
- [ ] All form inputs reference the Input L2 spec; all buttons reference the Button L2 spec
- [ ] Step transition animation uses `motion-duration-standard` and `motion-ease-standard` tokens
- [ ] `prefers-reduced-motion` disables step transition animation
- [ ] Progress is not lost if the user navigates back — form data persists in the step state

### Forbidden

An AI agent working on Onboarding Flow must never:

- Generate required steps that cannot be skipped without providing a mechanism to complete them later (trapping users in incomplete setup)
- Create a completion screen that immediately redirects without showing the user a success moment (the completion screen is a trust-building moment — do not skip it)
- Generate more than 7 steps in a single onboarding flow — longer flows require splitting into a separate "setup wizard" pattern with its own spec
- Add social login buttons (Google, GitHub) to an onboarding step unless the spec explicitly covers them (OAuth flows require their own L3 spec)
- Generate progress indicators that animate backward when the user clicks Back (progress bars should either stay or animate forward — never retreat)
- Validate all steps simultaneously on the final "Finish" action — each step validates independently when advancing
- Generate swipe or drag-based step navigation — this is a button-driven flow
- Add tracking pixels, analytics events, or third-party scripts to the generated output — these are infrastructure concerns outside the spec

### Prompt Seed

```
Generate an Onboarding Flow for [Product Name].

Reference: SDDn Delivery Spec [OB-L3-NNN] v[version].

Steps ([N] total):
1. Welcome — headline: "[headline]", subheadline: "[sub]", primary action: "Get started"
2. [Step name] — [description of what user does], required fields: [list], optional: [list], skippable: [yes/no]
3. [Step name] — [description], required fields: [list]
4. [Additional steps as declared in spec]
N. Completion — headline: "[headline]", summary of setup, CTA: "[Enter product label]"

Framework: [React / Vue — specify]

Token constraints:
- Container max-width: [value]
- Container background: color-surface-base
- Step indicator: current = color-action-primary, complete = color-semantic-success, upcoming = color-text-disabled
- Step heading (<h1>): typography-heading-xl
- Body text: typography-body-lg
- Step transition: motion-duration-standard + motion-ease-standard
- Footer padding: spacing-6
- Footer gap between actions: spacing-3

Component references (all must be ACTIVE):
- Form inputs: [INPUT-L2-NNN] v[version]
- Buttons (Next, Back, Skip, Finish): [BTN-L2-NNN] v[version]
- Form container: [FORM-L2-NNN] v[version]

Accessibility:
- Step indicator: aria-label="Step [N] of [N]: [step name]"
- Each step: one <h1> for step title
- Step transition: announce to screen reader via aria-live="polite"
- prefers-reduced-motion: no step transition animation

Do not generate: swipe navigation, simultaneous all-step validation on Finish, more than 7 steps, backward-moving progress indicators.
```
