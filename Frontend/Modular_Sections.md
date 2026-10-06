# Modular Sections

> Documented against `origin/main` @ `b7c9bbd` (2026-09-17).

Catalog of the reusable, prop-driven page sections in `components/modular_sections/`. Pages are built by composing these.

---

## Overview

Each section is a pure module with one exported `XProps` type, and that same type types its `*Content` object in `data/`. The rule is **reuse, then extend, then create** (frontend `CLAUDE.md` §17.6):

1. Use an existing section if one fits.
2. Otherwise add an optional, backward-compatible prop to an existing section.
3. Only then create a new one.

---

## Catalog

| Section | Purpose / notes |
|---------|-----------------|
| `Hero` | Page hero. `layout`: `image-bottom` (default), `image-diagonal`, `split-artwork`, `split-columns`. Optional `artworkScale`, `artworkFit`, animated background, primary/secondary CTA |
| `AccordionFeature` | Accordion beside media |
| `AppDownload` | App store promo |
| `AudienceTabbedFeature` | Tabs by audience |
| `CtaBanner` | Single CTA band |
| `CtaCards` *(feature/reusable-CTA)* | "Ready to Submit?" eyebrow + heading + outlined action cards. See [[Reusable_CTA]] |
| `EventCardGrid/` *(feature/event-page)* | Filterable event grid. See [[Events_Page]] |
| `EventCarousel` | Image-led event carousel (every field required, keyed by index). Not used for `/events` |
| `Faq` | FAQ accordion |
| `FeatureCards` | Card grid |
| `IconFeatureList` | Icon + text list ("By Us" illustrations) |
| `ImageGallery` | Single featured image (thumbnail strip removed in PR #49) |
| `InnovationPathwaysExplorer` | Interactive pathway explorer |
| `LabeledCardGroups` | Grouped cards with colour-coded group dots (VIP About) |
| `LogoGrid` / `LogoMarquee` | Partner logos |
| `MediaFeature` | Media + copy. Gains `tags`, `meta`, `imageAspect: "video"`, `ctaExternal` on `feature/event-page` |
| `NumberedSteps` | Numbered process |
| `PersonGrid` | Team cards with hover lift |
| `ProjectsCarousel` | Projects carousel |
| `ResourceLibrary` | Grouped resource carousels with detail modal (used by The Source and challenge resources) |
| `RoadmapTimeline` / `RoadmapTimelineList` | Roadmap milestones |
| `TabbedContent` / `TabbedOverview` | Tabbed copy |
| `TestimonialCarousel` / `VideoCarousel` / `VideoLightboxSection` | Media carousels, YouTube lightbox |
| `VIPCardsSection` | VIP card set |
| `CoreValuesFocusAreas` | Core values and program focus areas |
| `IdeasToImpact` | Ideas-to-impact band |
| `ImpactStats` | Stats band |
| `PartnerLogoGrid` / `PartnershipTiers` | Partner tiers (Challenge Partners & Amplifiers) |
| `PhaseCards` | Phase cards (pattern reused by `CtaCards` and `EventCardGrid`) |
| `SplitStatement` | Two-column statement |
| `TextCardGrid` | Text-only card grid |
| `ChallengeProcess/` | Steps + accordion + diagram, with `types.ts` |
| `NationalNetworkMap/` | Interactive network map: `hooks/`, `render/`, `utils/` (geometry, coordinates, paths) |

Folder-style sections (`ChallengeProcess/`, `NationalNetworkMap/`, `EventCardGrid/`) split the shell, client parts, and types into separate files. The `"use client"` boundary is kept as small as possible.

---

## Shared Conventions

- External links use `target="_blank" rel="noreferrer"`, a Lucide `ExternalLink` icon, and sr-only "Opens in a new tab".
- A CTA with no `href` renders as a plain non-navigating `Button`, never a dead link.
- `Reveal` handles scroll-in animation and respects reduced motion.
- Colours map from Figma hex to existing tokens, never new colours (§17.7).

---

## Related Notes

- [[Content_Data_Contract]]
- [[Pages_And_Routing]]
- [[Theming_Motion_Backgrounds]]
- [[04-Component-Frontend]]
