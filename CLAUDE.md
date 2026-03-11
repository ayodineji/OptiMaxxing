# Optimaxxing — Project Context for Claude

## What This Project Is
Optimaxxing is a Shopify dropshipping store selling men's self-optimization tools. The brand is built around a **7-protocol system** — each protocol targets a specific area of a man's physical appearance, health, or environment.

## Folder Structure
```
/HTML test code/        — Local HTML prototypes for visual testing in browser (no Liquid, no Shopify)
/Liquid Code for prod use/ — Production Shopify section files (.liquid) and page templates (.json)
```
Liquid files go into `Sections/` in the Shopify theme. JSON files go into `Templates/`.

## The 7 Protocols
| # | Handle | Name | Focus |
|---|--------|------|-------|
| 01 | `facemaxx-protocol` | Facemaxx | LED therapy, microcurrent, cryotherapy, skincare tools |
| 02 | `sleepmaxx-protocol` | Sleepmaxx | Sleep quality, mouth tape, blue light glasses, blackout gear |
| 03 | `gymmaxx-protocol` | Gymmaxx | Training recovery, mobility, output optimization |
| 04 | `framemaxx-protocol` | Framemaxx | Posture, jawline, physical frame & presence |
| 05 | `stylemaxx-protocol` | Stylemaxx | Grooming, clothing care, finishing details |
| 06 | `mindmaxx-protocol` | Mindmaxx | Focus, nootropics, cognitive environment tools |
| 07 | `cleanmaxx-protocol` | Cleanmaxx | Toxin elimination — PFAS, BPA, endocrine disruptors |

## Pages & Sections
- `optimaxxing-header.liquid` — Shared header (nav + cart drawer + footer + global CSS vars). Included on every page via JSON template.
- `optimaxxing-landing.liquid` — Homepage. Protocol tab switcher pulling from real Shopify collections.
- `optimaxxing-product.liquid` — Product page. Protocol auto-detected via product tags.
- `optimaxxing-collection.liquid` — Collection page for each protocol.
- `optimaxxing-bundles.liquid` — Bundles page. Bundle products are created in the Shopify Bundles app and referenced by handle.
- `optimaxxing-quiz.liquid` — 5-question quiz to recommend a starting protocol.
- `optimaxxing-about.liquid` — Mission/philosophy page.

## Design System
**Color Variables (defined in header):**
```css
--black:  #050505
--neon:   #C8FF00   /* lime green — primary accent */
--white:  #F5F5F5
--gray:   #141414
--mid:    #202020
--dim:    #888
--border: #1e1e1e
```
Special: Cleanmaxx uses `#00CFFF` (cyan) instead of neon green everywhere.

**Typography:**
- `'Bebas Neue'` — All headings, large display text
- `'Space Mono'` — Labels, nav links, tags, badges, buttons, prices
- `'Inter'` — Body copy, descriptions

**Layout patterns:**
- 2px gaps between grid cards (not a standard gutter — it's a design choice)
- `grid-template-columns: repeat(auto-fill, minmax(260px, 1fr))` for product grids
- Sections use `padding: 96px 48px` desktop, `padding: 56px 20px` mobile (768px breakpoint)

## Shopify-Specific Notes
- Products must be tagged with the protocol name (lowercase: `facemaxx`, `sleepmaxx`, etc.) for the product page to auto-detect the protocol breadcrumb.
- Custom metafield `custom.short_description` is used on product cards as a short tagline.
- Cart drawer uses Shopify Section Rendering API (`?sections=optimaxxing-header`) to refresh itself.
- Reviews are powered by **Judge.me** — dark theme overrides are included in the product section.
- The theme suppresses Shopify's default header via: `.section-header, header-drawer, .header-wrapper { display: none !important; }`
- Page templates reference sections like: `"type": "optimaxxing-header"` and `"type": "optimaxxing-landing"`.

## Brand Voice & Tone
- Direct, no-fluff, masculine
- "Optimization" is the core concept — everything is a "variable" to be adjusted
- Uses terms like: "protocol", "stack", "mog", "maxx", "variable", "tools"
- CTA language: "Add To Protocol", "Start The Protocol", "View All Protocols"
- Avoid: soft language, generic e-commerce copy, supplement-brand tone

## HTML Test Files
The `/HTML test code/` files are **standalone HTML** — they include all CSS inline, use Google Fonts CDN, and have no Shopify Liquid. They are used for rapid visual iteration before pushing to Shopify. Keep them in sync with their `.liquid` counterparts where possible.

