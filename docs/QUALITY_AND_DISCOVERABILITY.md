# Quality, accessibility and performance

> How the site is kept usable, fast and accurate, and what is checked before content or code goes live. [Back to the overview](../README.md) · Search details: [SEO and structured data](SEO_AND_STRUCTURED_DATA.md)

## Contents

- [Accessibility](#accessibility)
- [Performance](#performance)
- [Measured baseline](#measured-baseline)
- [Performance next steps](#performance-next-steps)
- [Publishing checklist](#publishing-checklist)
- [Medical information](#medical-information)

## Accessibility

| Area | What is in place |
| --- | --- |
| Language | The `<html lang>` attribute follows the visitor's choice (`en` or `bn-BD`), so screen readers use the right voice |
| Text alternatives | Every image field has an **SEO & Accessibility** panel (alt text with a 125-character guide, title, description) and shows how much is complete. Alt text lives on the media record, so one image has the same description everywhere |
| Alt text quality | The content audit flags missing, generic and duplicated alt text across the site |
| Accessible names | The search box is labelled; suggestions are announced as a list box; the cart button reads "Shopping cart, 2 items"; each add-to-cart button names its product, and an unavailable product says so |
| Headings | The audit checks for exactly one H1 and no skipped heading levels. In the document editor, choosing a heading level sets the element that is published |
| Readability | Body text is pure black on light backgrounds; small text is set one pixel larger than the template it was based on; Bengali text gets more line height |
| Mobile | Tap targets for call, WhatsApp and location are pinned at the bottom of the screen; the navigation drawer has clear Menu and Category tabs |

Screen-reader and keyboard testing across every page hasn't been formally audited yet. That is on the [roadmap](ROADMAP.md).

## Performance

| Lever | Implementation |
| --- | --- |
| Server work | Pages render per request with lean, indexed queries that load only the fields they display. Public data readers are de-duplicated within a request |
| Payload size | `/shop` shows up to 8 products per category; listing pages load 25 products at a time with **Load More** instead of the whole catalogue |
| Images | Content images in WebP; the Next.js image optimizer serves AVIF or WebP depending on what the browser accepts; square product images keep the layout stable |
| Caching | Media is served with one-year immutable caching; static assets come through the CDN; HTML is not cached, so it is always current |
| Fonts | Loaded with `next/font`: self-hosted, with fallback metrics that keep layout shift from font swaps to a minimum |
| Video | Self-hosted video is streamed with HTTP Range support, so it starts and seeks without downloading the whole file |
| Client code | Interactive islands (filters, cart, gallery, chat) hydrate; the rest of the page is server HTML |
| Transport | gzip at the web server and HTTP/2 through the CDN |

### Targets

Performance goals follow Google's Core Web Vitals at the 75th percentile of visits:

| Metric | Goal |
| --- | --- |
| Largest Contentful Paint | ≤ 2.5 s |
| Interaction to Next Paint | ≤ 200 ms |
| Cumulative Layout Shift | ≤ 0.1 |

These are goals for measurement, not claims of achieved scores. The admin panel's **Core Web Vitals** screen runs PageSpeed Insights on chosen pages and keeps the history.

## Measured baseline

A performance audit on **18 November 2026** measured server response time at the origin for representative pages:

| Page | Server time |
| --- | --- |
| Home | 0.08–0.11 s |
| Shop | 0.11–0.14 s |
| Category | 0.04–0.06 s |
| Product | 0.03–0.04 s |

Per-request rendering is not a bottleneck at this scale; browser-side weight is where the next gains are.

## Performance next steps

The audit ranked the following work, measuring first and changing second:

1. **Measure in the field.** Add request and upstream timing to the access log, and collect mobile Core Web Vitals by page group (home, shop, category, product, blog).
2. **Trim universal CSS.** Legacy theme stylesheets loaded on every page are the largest shared cost. Remove unused rules or split them by page.
3. **Lighter media.** Re-encode the home guidance video and its poster, and serve a smaller header logo.
4. **Bounded queries.** Load related products and posts with limits instead of whole categories.
5. **Cache shared published data** with reliable invalidation on save.
6. **Avoid translation stalls.** A translation cache miss shouldn't hold up a Bengali page render; fall back immediately and fill the cache in the background.

Load More and the 8-products-per-category shop page shipped on the same day as the audit. Staff-chosen picks for each category followed on 22 November.

## Publishing checklist

Before a significant content or code change goes live:

| Area | Check |
| --- | --- |
| Layout | Mobile and desktop at common widths, including the header menu at 1440 px and wider |
| Navigation | Header links, category pages, product pages, breadcrumbs and search |
| Commerce | Prices, regular prices, contact-for-price, rental packages, refill ranges, stock and delivery wording |
| Content | Policy dates, contact details, medical wording, and Bengali copy where it matters |
| Images | Alt text, titles and file names, especially on new uploads |
| Discoverability | Title and description, canonical URL, `noindex` choices, JSON-LD (automatic or custom), and the page's sitemap eligibility |
| Ordering | Cart, checkout, the WhatsApp message, the notification email |
| Redirects | Any renamed URL redirects to its new address, with no chains |

Code releases additionally pass the automated gate described in [deployment and operations](DEPLOYMENT_AND_OPERATIONS.md).

## Medical information

Product descriptions and articles are general information. Equipment selection, therapy settings and patient care should be guided by a qualified healthcare professional and the manufacturer's instructions. Records that touch patient care carry a **medical review** state, and a record that hasn't passed review is kept out of the sitemap.
