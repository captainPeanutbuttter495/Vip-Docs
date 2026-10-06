# Accessibility

Accessibility requirements and tooling for the VIP site. Target: **WCAG 2.1 AA**, or the current university-required standard. From frontend `CLAUDE.md` §8.

---

## Requirements Checklist

Every component must cover:

- semantic HTML
- heading order
- keyboard navigation
- visible focus
- screen-reader labels
- form labels and errors
- contrast
- alt text
- reduced motion
- accessible dialogs and menus
- touch-target size
- responsive text and zoom
- no hover-only interactions

Never use clickable `div`s. Never remove a focus outline without a replacement.

---

## What's in Place

| Mechanism | Where |
|-----------|-------|
| Skip link to `#main-content` | `app/layout.tsx` |
| `<main tabIndex={-1}>` focus target | `app/layout.tsx` |
| axe-core violations logged in dev | `components/AxeReporter.tsx` |
| `eslint-plugin-jsx-a11y` | ESLint config |
| Focus trap for the mobile menu and modals | `hooks/use-focus-trap.ts` |
| Reduced-motion system | [[Theming_Motion_Backgrounds]] |
| External-link pattern (icon + sr-only "Opens in a new tab") | Sections that link out |
| No dead controls: CTA with no `href` → plain button, or none | `CtaCards`, `EventCard`, `ResourceLibrary` |
| sr-only group labels beside decorative dots | `LabeledCardGroups` |

---

## Patterns Used in My Work

- Filter chips use `<button aria-pressed>` inside `role="group"`. The count is announced with `aria-live="polite"`. See [[Event_Page]].
- Detail rows are `<dl>` term/definition pairs.
- Contrast is checked for new title colours, e.g. `#BD3547` on parchment ≈ 5.4:1. See [[Reusable_CTA]].
- Every PR is checked manually at base, 834px, 1024px, and 1440px, in light and dark themes.

---

## Related Notes

- [[Testing]]
- [[Theming_Motion_Backgrounds]]
- [[Navigation_Layout]]
