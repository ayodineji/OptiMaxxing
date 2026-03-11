# Optimaxxing — Copilot Instructions

## Project Overview
Optimaxxing is a Shopify dropshipping store for men's self-optimization tools. It is organized around a **7-protocol system**, where each protocol targets a specific area of a man's physical appearance, health, or environment.

## Repository Layout
```
/HTML test code/           — Standalone HTML files for local browser testing (no Liquid/Shopify)
/Liquid Code for prod use/ — Production Shopify section files (.liquid) and page JSON templates
```
- `.liquid` files → deploy to `Sections/` in the Shopify theme
- `.json` files → deploy to `Templates/` in the Shopify theme

## The 7 Protocols
| # | Collection Handle | Name | Products |
|---|-------------------|------|----------|
| 01 | `facemaxx-protocol` | Facemaxx | LED masks, gua sha, microcurrent, cryotherapy tools |
| 02 | `sleepmaxx-protocol` | Sleepmaxx | Mouth tape, blue light glasses, blackout masks, sleep trackers |
| 03 | `gymmaxx-protocol` | Gymmaxx | Recovery tools, mobility aids, training output gear |
| 04 | `framemaxx-protocol` | Framemaxx | Posture correctors, jawline tools, frame/presence gear |
| 05 | `stylemaxx-protocol` | Stylemaxx | Grooming tools, clothing care, finishing accessories |
| 06 | `mindmaxx-protocol` | Mindmaxx | Nootropic tools, focus aids, cognitive environment products |
| 07 | `cleanmaxx-protocol` | Cleanmaxx | PFAS/BPA filters, water purifiers, toxin-elimination tools |

## Section Files
| File | Purpose |
|------|---------|
| `optimaxxing-header.liquid` | Shared nav, cart drawer, footer, and all global CSS variables |
| `optimaxxing-landing.liquid` | Homepage with hero, protocol tabs, product grids, and social proof |
| `optimaxxing-product.liquid` | Product page with variant picker, ATC, tabs, and related products |
| `optimaxxing-collection.liquid` | Per-protocol collection listing page |
| `optimaxxing-bundles.liquid` | Bundles page — references bundle products by Shopify handle |
| `optimaxxing-quiz.liquid` | 5-question quiz that recommends a starting protocol |
| `optimaxxing-about.liquid` | Mission/philosophy/manifesto page |

## Design System

### CSS Variables (defined in `optimaxxing-header.liquid`)
```css
--black:  #050505   /* page background */
--neon:   #C8FF00   /* primary accent — lime green */
--white:  #F5F5F5   /* body text */
--gray:   #141414   /* card backgrounds */
--mid:    #202020   /* hover states */
--dim:    #888      /* secondary text */
--border: #1e1e1e   /* dividers */
```
> **Exception:** Cleanmaxx (Protocol 07) uses `#00CFFF` (cyan) wherever neon green appears.

### Typography
| Font | Use |
|------|-----|
| `'Bebas Neue'` | All headings and large display text |
| `'Space Mono'` | Labels, nav, badges, buttons, prices, tags |
| `'Inter'` | Body copy, descriptions |

### Layout Rules
- **2px gap** between grid cards — intentional design choice, not a gutter error
- Product grids: `grid-template-columns: repeat(auto-fill, minmax(260px, 1fr))`
- Section padding: `96px 48px` desktop / `56px 20px` mobile (breakpoint: `768px`)

## Shopify-Specific Conventions
- Products are **tagged** with the lowercase protocol name (`facemaxx`, `sleepmaxx`, etc.) so the product page auto-detects the breadcrumb and protocol label.
- Short tagline per product: custom metafield `custom.short_description`.
- Cart drawer refreshes via **Shopify Section Rendering API**: `?sections=optimaxxing-header`.
- Reviews use **Judge.me** — dark theme CSS overrides live in `optimaxxing-product.liquid`.
- Shopify's default header is hidden globally:
  ```css
  .section-header, header-drawer, .header-wrapper { display: none !important; }
  ```
- Bundles are created in the **Shopify Bundles app** and referenced by product handle in `optimaxxing-bundles.liquid`.

## Brand Voice
- **Tone:** Direct, masculine, zero fluff
- **Core concept:** Everything is a *variable* that can be optimized
- **Key terms:** protocol, stack, variable, tools, mog, maxx
- **CTAs:** "Add To Protocol", "Start The Protocol", "View All Protocols", "Take The Quiz"
- **Avoid:** soft language, generic e-commerce copy, supplement-brand tone, passive voice

## HTML Prototype Files
Files in `/HTML test code/` are fully self-contained HTML — inline CSS, Google Fonts CDN, no Liquid. Used for fast visual iteration before pushing changes to Shopify. Keep them reasonably in sync with their `.liquid` counterparts.

