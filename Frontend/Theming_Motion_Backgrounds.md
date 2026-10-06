# Theming, Motion & Backgrounds

Brand tokens, light/dark theming, the motion-preference system, and the animated background registry.

---

## Theming

- `next-themes` with `attribute="class"` and `defaultTheme="system"`.
- Tailwind 4 tokens are defined in `app/globals.css`:
  - shadcn semantic tokens (`background`, `primary`, `card`, …)
  - VIP brand tokens: `onyx`, `carbon`, `jet`, `parchment`, `light-grey`, `cta`, `cta-dark`, `logo-yellow`
  - palettes: `cyan-*`, `wisteria-*`, `orange-*`, `sky-*`
- Typical page shell: `bg-parchment text-onyx dark:bg-onyx dark:text-light-grey`. Some bands (carbon, onyx) stay fixed-contrast in both themes.
- Figma is often dark-mode only. Colours are mapped **by hex** to existing tokens (§17.7). New brand colours are never invented.

## Motion — `MotionProvider` + `use-motion-preference`

- Site-wide motion follows the device's `prefers-reduced-motion`. There is no site-level override. It is published two ways:
  - React: `useReducedMotionPreference()`
  - Motion elements: `<MotionConfig reducedMotion>`
  - Raw CSS animation is covered by a media query in `globals.css`.
- Animated backgrounds have a **separate** play/pause state (`BackgroundMotionToggle` in heroes), persisted in `localStorage` under `vip-bg-motion`. A reduced-motion device keeps backgrounds frozen no matter what.
- `Reveal` (scroll-in) respects both.

## Animated Backgrounds — `components/ui/backgrounds/`

`registry.ts` maps a `BackgroundVariant` to a config:

| Variant | Component | Notes |
|---------|-----------|-------|
| `dotted-glow` | `DottedGlowBackground` | Canvas |
| `ripple` | `BackgroundRippleEffect` | Click-driven, interactive |
| `constellation` | `ConstellationBackground` | Canvas |
| `grid-scan` | `GridScanBackground` | WebGL (three + postprocessing). Uses a `staticImage` fallback instead of freezing, so no GPU context is held |

Config fields:

- `Component`
- `defaultProps`: brand colours as literals, because canvas can't read Tailwind classes
- `motionProps(animate)`
- `staticImage`
- `interactive`

To add an effect, add a union member and a registry entry. No other file changes.

---

## Key Files

| File | Purpose |
|------|---------|
| `app/globals.css` | Tokens |
| `components/theme-provider.tsx` | next-themes wrapper |
| `components/MotionProvider.tsx`, `hooks/use-motion-preference.ts` | Motion state |
| `components/ui/backgrounds/*` | Background registry and effects |
| `components/ui/BackgroundMotionToggle.tsx` | Pause/play control |

---

## Related Notes

- [[Accessibility]]
- [[Modular_Sections]]
- [[Navigation_Layout]]
