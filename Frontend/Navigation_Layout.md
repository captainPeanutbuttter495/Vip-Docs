# Navigation & Layout

The root layout and the global chrome around every page: NavBar, Footer, ScrollIndicator, skip link, and providers.

---

## Overview

`app/layout.tsx` wraps every route in this order:

```
<html lang="en" class="font vars">
  <ThemeProvider attribute="class" defaultTheme="system" enableSystem>
    <MotionProvider>
      Skip link → #main-content
      <NavBar />
      <ScrollIndicator />
      <main id="main-content" tabIndex={-1}>{children}</main>
      <Footer />
      <AxeReporter />   (dev only)
```

Fonts:

- IBM Plex Sans (`--font-sans`)
- IBM Plex Mono (`--font-geist-mono`)
- Gotham, loaded locally from `app/fonts/Gotham/*.otf` (`--font-gotham`)

Site metadata title: "Global HSI Equity Innovation Hub".

---

## NavBar — `components/global/NavBar/`

| File | Role |
|------|------|
| `NavData.ts` | `navItems: NavItem[]`. A `NavItem` is either a `NavDropdownItem` (with `subItems`) or a `NavLinkItem`. An item with no `href` renders unlinked while its page is unfinished |
| `NavBar.tsx` | Desktop bar |
| `NavDropdown.tsx` / `NavSubItem.tsx` | Dropdown panels |
| `MobileMenu.tsx` | Mobile menu (uses `use-focus-trap`) |

Current top level (`origin/main`):

- **About:** Who We Are, Meet the Team & Students. Gallery is commented out.
- **Engage With Us**
- **National Innovation Challenge:** Challenge Partners & Amplifiers, Innovation Support Ecosystem
- **Virtual Innovation Platform:**
  - VIP
  - Catalyst Zone: National Challenge, with Get Started, Innovation Pathways, and Resources → Side Hustle Bootcamp
  - The Source is commented out.

## Footer — `components/global/Footer/`

`Footer.tsx` and `FooterNav.tsx` link to the resources and legal pages.

## Other Global Components

| Component | Role |
|-----------|------|
| `AnnouncementBanner.tsx` | Site-wide banner |
| `ScrollIndicator/` | Scroll progress |
| `components/ui/Breadcrumb.tsx` | Shared breadcrumb (`nav aria-label="Breadcrumb"`) |

---

## Related Notes

- [[Pages_And_Routing]]
- [[Theming_Motion_Backgrounds]]
- [[Accessibility]]
