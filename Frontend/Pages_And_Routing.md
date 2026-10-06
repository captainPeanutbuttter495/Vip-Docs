# Pages and Routing

Page map for `vip-web-frontend`: Next.js App Router, where each route is a thin `page.tsx` that composes sections from a `data/` content object.

> Documented against `origin/main` @ `b7c9bbd` (2026-09-17). The local `main` checkout is behind `origin/main`, so run `git pull` before comparing.

---

## Overview

Every route file does three things:

1. Exports `metadata`.
2. Imports `*Content` objects from `data/<page>Data.tsx`.
3. Renders `<Section {...content} />` inside a theme-aware shell, e.g. `bg-parchment text-onyx dark:bg-onyx dark:text-light-grey`.

No route fetches data today.

---

## Route Map (`origin/main`)

| Route | Content source | In nav? |
|-------|----------------|---------|
| `/` | `data/homeData.tsx` | Logo |
| `/about` | `data/aboutData.tsx` | About → Who We Are |
| `/team` | `data/teamData.tsx` | About → Meet the Team & Students |
| `/gallery` | `data/galleryData.tsx` | **Commented out** of nav |
| `/engage-with-us` | `data/engageWithUsData.tsx` | Top level |
| `/innovation-challenge` | `data/innovationChallengeData.tsx` | National Innovation Challenge |
| `/challenge-partners-amplifiers` | `data/partnersAmplifiersData.tsx` | National Innovation Challenge → Challenge Partners & Amplifiers |
| `/innovation-support-ecosystem` | `data/innovationSupportEcosystemData.tsx` | National Innovation Challenge → Innovation Support Ecosystem |
| `/vip` | `data/vipData.tsx` | Virtual Innovation Platform → VIP |
| `/the-source` | `data/theSourceData.tsx` | **Commented out** of nav (URL only) |
| `/national-steam-innovation-challenge` | `data/nationalSteamChallenge.ts` | VIP → Catalyst Zone: National Challenge |
| `…/get-started` | `data/getStartedData.ts` | Catalyst Zone → Get Started |
| `…/innovation-pathways` | `data/innovationPathway.ts` | Catalyst Zone → Innovation Pathways |
| `…/resources` | `data/challengeResourcesData.ts` | Catalyst Zone → Resources |
| `…/resources/side-hustle-bootcamp` | `data/sideHustleBootcamp.tsx` | Resources → Side Hustle Bootcamp |
| `/resources` | `data/resourcesData.ts` | Footer |
| `/resources/accessibility-statement` | `data/accessibilityData.ts` | Footer |
| `/resources/privacy-policy` | `data/privacyPolicyData.ts` | Footer |
| `/resources/terms-of-use` | `data/termsOfUseData.ts` | Footer |
| `/resources/copyright-and-licensing` | `data/copyrightLicensingData.ts` | Footer |

### On unmerged branches

| Route | Branch | Status |
|-------|--------|--------|
| `/events` | `feature/event-page` | Draft PR. URL only. See [[Events_Page]] |

---

## Key Files

| File | Purpose |
|------|---------|
| `app/layout.tsx` | Root layout. See [[Navigation_Layout]] |
| `components/global/NavBar/NavData.ts` | Nav tree. A `NavItem` is either a dropdown or a link. An item with no `href` renders unlinked |
| `data/*` | Page content objects |

---

## Related Notes

- [[Content_Data_Contract]]
- [[Modular_Sections]]
- [[Navigation_Layout]]
- [[04-Component-Frontend]]
