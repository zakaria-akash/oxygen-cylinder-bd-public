<div align="center">

# Oxygen Cylinder BD

### A self-hosted medical-equipment storefront, rental service and content platform for Bangladesh

[**www.oxygencylinderbd.com**](https://www.oxygencylinderbd.com/)

[![Status](https://img.shields.io/badge/Status-Live-1F7A45?style=for-the-badge)](#project-status)
[![Source](https://img.shields.io/badge/Source-Private-475569?style=for-the-badge)](docs/SHOWCASE_BOUNDARY.md)
[![Next.js](https://img.shields.io/badge/Next.js-16.4-000000?style=for-the-badge&logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.8-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![MongoDB](https://img.shields.io/badge/MongoDB-8_·_Mongoose_9-47A248?style=for-the-badge&logo=mongodb&logoColor=white)](https://mongoosejs.com/)
[![Admin](https://img.shields.io/badge/Admin-Ant_Design_6-1677FF?style=for-the-badge&logo=antdesign&logoColor=white)](docs/CONTENT_MANAGEMENT.md)
[![Editor](https://img.shields.io/badge/Editor-Tiptap_3-1F2937?style=for-the-badge)](docs/CONTENT_MANAGEMENT.md#the-document-editor)
[![Tests](https://img.shields.io/badge/Tests-103_passing-1F7A45?style=for-the-badge)](docs/ENGINEERING_QUALITY.md)
[![Hosting](https://img.shields.io/badge/Hosting-VPS_·_Nginx_·_Cloudflare-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](docs/DEPLOYMENT_AND_OPERATIONS.md)

**Public engineering showcase** for a closed-source production application · built and launched October–November 2026

<br />

<img src="docs/screenshots/storefront/home-hero.jpg" alt="Oxygen Cylinder BD home page: a green hero headline, a search bar, and a rotating 3D display of medical equipment" width="100%" />

</div>

---

## Contents

- [Overview](#overview)
- [Project at a glance](#project-at-a-glance)
- [Highlights](#highlights)
- [A look at the product](#a-look-at-the-product)
- [Implementation scale](#implementation-scale)
- [Technology stack](#technology-stack)
- [System architecture](#system-architecture)
- [Private codebase map](#private-codebase-map)
- [Route families](#route-families)
- [Domain model](#domain-model)
- [Ordering without accounts](#ordering-without-accounts)
- [The admin panel](#the-admin-panel)
- [Search discoverability](#search-discoverability)
- [Security](#security)
- [Operations](#operations)
- [Project status](#project-status)
- [Documentation](#documentation)
- [Screenshot gallery](#screenshot-gallery)
- [Public repository boundary](#public-repository-boundary)

## Overview

Oxygen Cylinder BD is a Dhaka-based supplier of medical oxygen cylinders, oxygen concentrators, BiPAP and CPAP machines, hospital beds, wheelchairs, physiotherapy machines and home-care equipment. Customers can **buy, rent or refill** equipment and are served across Bangladesh.

Until November 2026 the business ran on a WordPress and WooCommerce site. This project replaced it with a single **Next.js 16 App Router** application. The application contains the public storefront, a guest ordering flow, a bilingual interface, a content platform for buying guides and articles, and a purpose-built admin panel. Work started on 4 October 2026. Twenty-nine days later, on **2 November 2026**, the application took over `www.oxygencylinderbd.com` with the live catalogue and its URLs intact. It kept growing through 128 commits by 25 November.

The application is self-hosted on a VPS behind Nginx and Cloudflare. Its MongoDB database and GridFS media store live on the same private host. This repository documents the product, the architecture and the engineering decisions. The source code is private.

## Project at a glance

| Area | Detail |
| --- | --- |
| **Live site** | [www.oxygencylinderbd.com](https://www.oxygencylinderbd.com/), served by this application since 2 Nov 2026 |
| **Catalogue** | 239 published products in 35 categories, as shown on the live `/shop` page on 26 Nov 2026 |
| **Service model** | Purchase, fixed-package rental (for example 5, 10 or 30 days), and cylinder refill, per product |
| **Ordering** | Guest cart and checkout with cash on delivery, plus WhatsApp and phone ordering. No customer accounts and no card payments, by design |
| **Audiences** | Patients, families and caregivers; clinics and care teams; the company's content and operations staff |
| **Languages** | English with a Bengali interface mode on the same URLs (see [localization](docs/LOCALIZATION.md)) |
| **Admin panel** | 44 admin page routes covering the catalogue, page studios, legal pages, blog, FAQ, orders, live chat, inbox, media library, redirections, SEO workspace, analytics, project audit and settings |
| **Delivery** | 128 commits from 4 Oct to 25 Nov 2026; reviewed releases with automatic rollback |

## Highlights

- **A WordPress-to-Next.js migration that kept the search history.** The WooCommerce catalogue (products, categories and images) was imported into MongoDB and GridFS. The `/product/<slug>/` and `/product-category/<slug>/` URL shapes were kept, including the trailing slash. A layered resolver then sends retired WordPress addresses to their new pages with permanent redirects. See [the migration case study](docs/WORDPRESS_MIGRATION.md).
- **Server-authoritative ordering.** The checkout sends product references, quantities and the chosen options, never a price the server trusts. The server rebuilds every line from the live catalogue, snapshots it as immutable order history, and de-duplicates double submissions with an idempotency key. If the notification email fails, the order is still kept and the email can be retried. See [commerce and orders](docs/COMMERCE_AND_ORDERS.md).
- **Buy, rent and refill on one product.** One product record supports a purchase price, discounted regular price, contact-for-price, fixed rental packages and a refill price range. The storefront, the cart rules and the order server share a single set of rules.
- **A Word-style document editor for staff.** The content team writes guides and product descriptions in a Tiptap/ProseMirror editor with tables, images, video and paste-from-Word. Everything is stored as validated content blocks, never as raw HTML. See [content management](docs/CONTENT_MANAGEMENT.md).
- **One image, one set of details.** Alt text, title, description and SEO file name live on the media record. They follow the image into every page, editor and JSON-LD block, and edits use revision checks to prevent lost updates.
- **SEO treated as part of the runtime.** A single indexable-page registry drives the sitemap, `robots.txt`, the admin page inventory and the content audit. Every page renders its JSON-LD through shared builders, and editors can replace a page's schema with validated custom JSON-LD. See [SEO and structured data](docs/SEO_AND_STRUCTURED_DATA.md).
- **Privacy-first visitor analytics built in.** A same-origin beacon records page views without cookies and without storing IP addresses. It uses a GeoIP lookup for countries and networks, and classifies bot and data-center traffic so it can be hidden. A Google Analytics 4 dashboard sits alongside it. See [analytics](docs/ANALYTICS.md).
- **Deny-by-default administration.** Three roles. Every page passes a proxy gate, every API handler re-checks the session, and new endpoints stay closed to Content Admins unless a handler opts in. See [security architecture](docs/SECURITY_ARCHITECTURE.md).
- **Releases that can't half-ship.** Each release goes through the same gate: a fresh database backup, a clean dependency audit, the test suite and a production build, then an atomic switch. A failed smoke check rolls back automatically. See [deployment and operations](docs/DEPLOYMENT_AND_OPERATIONS.md).

## A look at the product

<table>
  <tr>
    <td width="33%"><img src="docs/screenshots/storefront/category-listing-filters.jpg" alt="Category page with a sort and filter sidebar, price bands with counts, and product cards" /><br /><sub><b>Category page</b>: sorting, price bands with live counts, stock, rental and refill filters</sub></td>
    <td width="33%"><img src="docs/screenshots/storefront/product-rental-packages.jpg" alt="Rental product page showing 5, 10 and 30 day rental packages" /><br /><sub><b>Rental product</b>: fixed rental packages chosen on the product page</sub></td>
    <td width="33%"><img src="docs/screenshots/storefront/checkout-cash-on-delivery.jpg" alt="Checkout page with order summary and a cash on delivery button" /><br /><sub><b>Guest checkout</b>: cash on delivery, with WhatsApp and phone alternatives</sub></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/admin/product-editor.jpg" alt="Admin product editor mirroring the public product page layout" /><br /><sub><b>Product editor</b>: mirrors the public page; multi-category placement, buy, rent and refill pricing</sub></td>
    <td><img src="docs/screenshots/admin/legal-document-editor.jpg" alt="Word-style document editor with a ribbon toolbar and formatted policy text" /><br /><sub><b>Document editor</b>: Word-style editing saved as structured content blocks</sub></td>
    <td><img src="docs/screenshots/admin/seo-review.jpg" alt="SEO review modal with a score of 98, keyphrases, title and meta description" /><br /><sub><b>SEO Review</b>: live score, keyphrases, snippet fields and checks in every editor</sub></td>
  </tr>
</table>

More screens are in the [screenshot gallery](#screenshot-gallery) below and in [docs/SCREENSHOTS.md](docs/SCREENSHOTS.md).

## Implementation scale

Measured on the private repository at commit `7090601` (25 Nov 2026).

| Measure | Private application |
| --- | ---: |
| TypeScript/JavaScript source files in `src/` | 452 (175 `.ts`, 167 `.tsx`, 84 `.js`, 26 `.jsx`) |
| Lines of application code (TS/JS) | ≈ 54,000 |
| Stylesheets (CSS Modules and global CSS) | 72 files, ≈ 9,500 lines |
| React components | 118 |
| Server-side library modules (`src/lib`) | 118 |
| Mongoose models | 21, plus GridFS and two native-driver collections |
| Route handlers | 96 (76 admin, 19 public and utility, 1 media stream) |
| Page routes | 63 (19 public, 44 admin) |
| Routes in the production build | 164 |
| Operational scripts (imports, guarded migrations, sync, tests) | 86 files, ≈ 10,400 lines |
| Automated tests | 103 passing in 15 suites, plus a headless-browser language test |

## Technology stack

| Layer | Technology | How it is used |
| --- | --- | --- |
| Framework | Next.js `16.4.6` (App Router), React `19.1` | Server Components for data and metadata, client components for interaction, route handlers for the REST API, `proxy.ts` for gating and redirects |
| Language | TypeScript `5.8` with typed JavaScript modules | Shared types across server, client and tests |
| Public UI | Bootstrap `5.3`, CSS Modules, React Icons | Responsive storefront in a deep-green theme; Roboto body text and Barlow headings |
| Admin UI | Ant Design `6.4`, dnd-kit | Data-dense admin screens, drag-and-drop ordering |
| Rich content | Tiptap `3.31` (ProseMirror) | Word-style document editor with tables, alignment, sub/superscript, typography and unique block IDs |
| Data | MongoDB `8` (production), MongoDB Atlas (development), Mongoose `9.11`, MongoDB driver `7.7` | Document models, aggregation for analytics, GridFS for media |
| Validation | Zod `4.6` | Request schemas for content, settings, orders, contact enquiries and redirects |
| Auth | jose `6.2` (HS256 JWT), Node `crypto` scrypt | Admin sessions and password hashing |
| Email | Nodemailer `10.2` | Order notifications and replies from the team inbox |
| Geo and analytics | mmdb-lib `3.0` with DB-IP Lite databases | Country and network lookup for first-party visitor analytics |
| Google APIs (server-side only) | Cloud Translation v2, Analytics Data and Admin APIs, Search Console, Merchant Center, PageSpeed Insights | Bengali translation, GA4 dashboards, search and performance diagnostics |
| Runtime and hosting | Node.js 24 LTS, systemd, Nginx, Cloudflare, Let's Encrypt | Self-hosted VPS; HTTPS at the edge and the origin |
| Testing | Node's built-in test runner, headless Chrome over the DevTools protocol | Offline unit tests, browser checks and release smoke tests |

## System architecture

```mermaid
flowchart LR
  subgraph Clients
    V["Visitors<br/>desktop and mobile"]
    S["Staff<br/>admin panel"]
  end

  subgraph Edge
    CF["Cloudflare<br/>TLS, bot challenges"]
  end

  subgraph VPS["Self-hosted VPS"]
    NG["Nginx<br/>TLS, HSTS, gzip, rate limits"]
    subgraph APP["Next.js 16 application (systemd)"]
      PX["proxy.ts<br/>admin gate, redirects, language"]
      SC["Server Components<br/>pages, metadata, JSON-LD"]
      API["Route handlers<br/>public and admin APIs"]
      LIB["Domain modules<br/>orders, media, SEO, translation, analytics"]
    end
    DB[("MongoDB 8<br/>documents and GridFS media")]
  end

  EXT["Google APIs<br/>Translation, GA4, Search Console, PageSpeed"]
  MAIL["SMTP"]

  V --> CF
  S --> CF
  CF --> NG --> PX
  PX --> SC
  PX --> API
  SC --> LIB
  API --> LIB
  LIB --> DB
  LIB -.->|server-side only| EXT
  LIB -.-> MAIL
```

- **One deployable application.** The storefront, the admin panel, the APIs, the media server and the analytics endpoint are one Next.js service. They share the same models, validation and URL helpers.
- **Two read paths.** Public pages read MongoDB in Server Components so that the title, canonical URL, JSON-LD and redirect decisions are made before any client code runs. Interactive parts (search suggestions, Load More, the cart, chat) call route handlers.
- **Cheap, fail-open middleware.** `proxy.ts` gates admin pages, applies database-managed redirects with a one-second query limit, and sets the language header. If the redirect store is slow or unavailable, the request carries on normally.
- **External services stay on the server.** No API key reaches the browser. Translation, GA4, Search Console and PageSpeed calls run in route handlers with timeouts.

Read more in [architecture](docs/ARCHITECTURE.md).

## Private codebase map

The real organization of the private repository, shared as a map rather than a source dump.

```text
oxygen-cylinder-bd/                      (private)
├── src/
│   ├── app/
│   │   ├── (site)/                      # Public storefront route group (19 pages)
│   │   │   ├── page.tsx                 # Home page
│   │   │   ├── (inner)/shop/            # /shop, the protected "All Products" default category
│   │   │   ├── (inner)/product-category/[slug]/
│   │   │   ├── (inner)/product/[slug]/  # WooCommerce-compatible product URLs
│   │   │   ├── (inner)/cart/ checkout/  # Guest ordering (noindex, nofollow)
│   │   │   ├── (inner)/blog/ faq/ about-us/ contact-us/ career/
│   │   │   ├── (inner)/[legalSlug]/     # Privacy, terms, warranty, delivery, refund, how to buy, affiliate
│   │   │   └── [...notFound]/           # Legacy WordPress URL resolution before any 404
│   │   ├── (admin)/admin/               # Sign-in and the protected admin panel (44 pages)
│   │   ├── api/                         # 95 route handlers: admin, orders, chat, contact, search, media, analytics
│   │   ├── media/[filename]/            # Public uploads served by their SEO file name
│   │   ├── sitemap.ts                   # Generated per request from the indexable page registry
│   │   └── robots.ts                    # Includes rules for AI crawlers, with a stale-while-revalidate fallback
│   ├── components/                      # 118 components: storefront, home, header, admin, chat, analytics, SEO
│   ├── lib/                             # 118 modules
│   │   ├── seo/                         # Metadata, JSON-LD builders, custom schema, sitemap registry, legacy URL map
│   │   ├── translation/                 # Translation engine, allowlist catalogue, provider adapter
│   │   ├── visitors/                    # Beacon store, bot and data-center classification, GeoIP, dashboards
│   │   └── *.ts / *.js                  # Auth, orders, rentals, media, redirects, content blocks, settings
│   ├── models/                          # 21 Mongoose models and shared SEO, review and draft schemas
│   ├── i18n/                            # Language provider, switcher, EN/BN dictionaries, locale helpers
│   ├── features/seo/analysis/           # Shared SEO document analysis used by editors and audits
│   └── proxy.ts                         # Admin page gate, managed redirects, language routing
├── scripts/                             # 86 scripts: WordPress import, guarded migrations, catalogue sync, tests
├── public/                              # Fonts, bundled images, verification files
└── next.config.ts                       # Trailing-slash compatibility, noindex headers for cart and checkout
```

## Route families

| Family | Examples | Engineering notes |
| --- | --- | --- |
| Storefront | `/`, `/shop`, `/product-category/[slug]/`, `/product/[slug]/` | URL shapes kept from WooCommerce; category pages list products in the order set in the admin |
| Ordering | `/cart`, `/checkout`, `POST /api/orders` | Guest only; cart and checkout send `X-Robots-Tag: noindex, nofollow` |
| Content | `/blog`, `/blog/[slug]`, `/faq`, `/about-us`, `/contact-us`, `/career`, `/[legalSlug]` | Page content managed in the admin panel; slug history keeps old links working |
| Discovery | `/sitemap.xml`, `/robots.txt`, `GET /api/search/suggestions` | Sitemap and robots are generated per request, so they can never break a build; suggestions are rate-limited |
| Media | `/api/media/[id]/[[...seo]]`, `/media/[filename]`, `/api/image-assets/[...path]` | GridFS streaming with HTTP Range support and one-year immutable caching |
| Engagement | `/api/chat/*`, `/api/contact/messages`, `/api/visitors/collect`, `/api/translate` | Rate-limited, size-capped and same-origin where it applies |
| Admin | `/admin/*` (44 pages), `/api/admin/*` (76 handlers) | Proxy gate plus a session and role check in every handler |
| Legacy | `/?s=…`, `/bn/*`, old WordPress slugs | Permanent redirects to the current page |

## Domain model

| Domain | Models and collections |
| --- | --- |
| Catalogue | `ProductCategory`, `Product`, `ShopPageSettings` (the protected All Products category) |
| Ordering | `Order` with immutable line snapshots, rental terms and email-notification state |
| Content | `PageContent`, `BlogPost`, `FaqCategory`, `FaqItem`, `PageSharing`, `SiteSettings` |
| Conversations | `ChatSession`, `ContactConversation` |
| Discoverability | `SeoAdminSettings`, `SeoPageSpeedAudit`, `StructuredDataSettings`, `Redirection`, `RedirectRule` |
| Operations | `ProjectAuditRun`, `ProjectAuditWorkItem`, `SeedTombstone` |
| Localization | `TranslationCache` |
| Native driver | GridFS `uploads` bucket, `public_asset_metadata`, `visitor_pageviews` (400-day TTL) |

```mermaid
erDiagram
  PRODUCT_CATEGORY ||--o{ PRODUCT : "main category"
  PRODUCT_CATEGORY }o--o{ PRODUCT : "also listed in"
  PRODUCT ||--o{ ORDER_LINE : "snapshotted into"
  ORDER ||--|{ ORDER_LINE : contains
  PRODUCT }o--o{ MEDIA_FILE : "images reference"
  BLOG_POST }o--o{ MEDIA_FILE : "images reference"
  PAGE_CONTENT }o--o{ MEDIA_FILE : "images reference"
  REDIRECTION }o--|| PUBLIC_PATH : "retired source"
```

Details, including the shared SEO, review and draft sub-schemas, are in [the data model](docs/DATA_MODEL.md).

## Ordering without accounts

```mermaid
sequenceDiagram
  autonumber
  participant B as Browser (cart in localStorage)
  participant O as POST /api/orders
  participant C as Catalogue (MongoDB)
  participant M as Mail server
  B->>O: product ids, quantities, rental package, contact details, idempotency key
  O->>O: rate limit, 48 KB body cap, schema validation
  O->>C: load live products and prices
  C-->>O: published products
  O->>O: rebuild lines, apply rental and delivery rules
  O->>C: insert order (unique idempotency key)
  O->>M: notify the team
  M-->>O: sent or failed (recorded on the order)
  O-->>B: order number
```

The browser never sets a price. Status changes only move forward (pending → confirmed → processing → delivered), and a paid order can't be cancelled. Rental orders carry their package, start and end dates and a returned date. See [commerce and orders](docs/COMMERCE_AND_ORDERS.md).

## The admin panel

| Area | What staff can do |
| --- | --- |
| Dashboard | See pending orders, unread messages, open chats, catalogue and media counts, and a visitor summary |
| Products | Browse by category, set the order with drag and drop, list a product in several categories, duplicate, import content from another product, save drafts |
| Categories | Edit each category page's title, introduction, buying guide, icon, public URL (old URLs redirect) and SEO; the default All Products category is locked |
| Page studios | Edit the Home, About, Blog index, Contact, Career, FAQ and Footer pages section by section, with inline links and emphasis |
| Legal pages | Edit seven policy and information pages in the document editor, with an approval state |
| Blog | Write posts with images, key takeaways, tags, slug history, SEO and editorial review |
| Orders, chat and inbox | Confirm and progress guest orders, reply to live chats, handle contact enquiries with priority and status |
| Media Library | Upload files, inspect where each file is used, edit image details, and remove unused files safely |
| Redirections | Manage 301/302/307/308 rules with query handling, hit counts and loop protection |
| SEO workspace | Overview, page inventory, content audit, structured data, Search Console, GA4, visitor analytics, Core Web Vitals |
| Project Audit | Read-only checks of security, reliability, performance and integrations, with saved snapshots and follow-ups |

When an error occurs, a modal explains the likely cause and what to do next, instead of a short toast. The admin panel has its own English/Bengali switch. See [content management](docs/CONTENT_MANAGEMENT.md).

## Search discoverability

- **One registry, many consumers.** `getIndexablePageRegistry()` lists every public record with the reasons it is or isn't indexable: unpublished, `noindex`, review incomplete, route incomplete, or canonical pointing elsewhere. The sitemap, `robots.txt`, the admin page inventory and the audit all read from it.
- **Live sitemap** (26 Nov 2026): 276 URLs with `lastmod`: 238 products, 26 categories, 8 static pages and 4 articles.
- **JSON-LD on every page** through shared builders: `Organization`/`Store`, `WebSite`, `Product` with `Offer` and `Brand`, `BreadcrumbList`, `CollectionPage` with `ItemList`, `BlogPosting`, `FAQPage` and `ContactPage`. Editors can paste a custom schema; it is validated, and `<` is escaped so pasted content can't close the script element.
- **AI crawler policy.** `robots.txt` withholds pages that editors mark `noindex` from named AI crawlers (GPTBot, ClaudeBot, PerplexityBot, CCBot and others), while search engines can still fetch them and see the `noindex`.
- **URL continuity.** Product slugs, category URLs and post slugs keep a history; changing one redirects the old address permanently.

See [SEO and structured data](docs/SEO_AND_STRUCTURED_DATA.md) and [quality and discoverability](docs/QUALITY_AND_DISCOVERABILITY.md).

## Security

| Control | Implementation |
| --- | --- |
| Admin accounts | Defined in the server environment, never in the database; scrypt hashes; 12-character minimum; constant-time comparison; equal response time for unknown accounts |
| Sessions | HS256 JWT with issuer and audience, 7-day expiry, `httpOnly` `SameSite=Lax` cookie (`Secure` in production). The token is bound to a keyed digest of the current password, so a password change signs out every session |
| Authorization | Super Admin, Admin and Content Admin roles. The proxy page gate and each API handler check them independently; Content Admins can't delete or unpublish; file deletion and media cleanup are Super Admin only |
| Public endpoints | Per-client rate limits, body-size caps, same-origin checks, Zod schemas, a honeypot on the contact form and idempotent order creation |
| Content | Structured blocks instead of stored HTML; an allowlist parser for pasted documents; validated link and image sources; escaped JSON-LD |
| Transport | Cloudflare (Full strict TLS) in front of Nginx; HSTS for one year; no `X-Powered-By`; the application and the database listen only on the host |
| Supply chain | A release is blocked unless the dependency audit is clean and the tests pass |

See [security architecture](docs/SECURITY_ARCHITECTURE.md) and the [security policy](SECURITY.md).

## Operations

```mermaid
flowchart LR
  A["Workstation<br/>tested commit, archive and checksum"] --> B["Build gate on the server<br/>fresh backup, npm ci, dependency audit, tests, build"]
  B -->|any step fails| X["Stop: live site untouched"]
  B --> C["Atomic switch<br/>new release becomes current"]
  C --> D{"Smoke checks"}
  D -->|pass| E["Live"]
  D -->|fail| R["Automatic rollback<br/>to the previous release"]
```

- Each release is an immutable directory named after its commit; the live site always has rollback targets on disk.
- Database migrations never ride along with code. They are separate scripts that default to a dry run, need an explicit `--apply`, and are re-run as a dry run afterwards to prove nothing is left.
- MongoDB is backed up nightly as compressed archives with SHA-256 checksums.
- After each switch the team checks every category page, every product page and every header link against the live database, and compares JSON-LD output before and after.

See [deployment and operations](docs/DEPLOYMENT_AND_OPERATIONS.md).

## Project status

| Capability | Status | Notes |
| --- | --- | --- |
| Public storefront, catalogue and content pages | Live | Replaced the WordPress site on 2 Nov 2026 |
| Guest cart, checkout and order management | Live | Cash on delivery; WhatsApp and phone ordering alongside |
| Admin panel (catalogue, pages, blog, legal, media, orders, chat, inbox) | Live | Three roles with deny-by-default APIs |
| Redirections and legacy WordPress URL handling | Live | Layered: application rules, slug history, database rules |
| SEO workspace, structured data, custom JSON-LD | Live | Custom schema field available in every SEO Review |
| First-party visitor analytics | Live | No cookies, no stored IP addresses |
| Google Analytics 4, Search Console, PageSpeed modules | Available | Each one becomes active when its Google credentials are configured |
| Bengali interface | Implemented | Interface switch, Bengali typography and translation pipeline; translated copy depends on a configured translation provider, with English shown as the fallback |
| Customer accounts, online card payment | Not in scope | Orders are confirmed by phone or WhatsApp; payment is cash on delivery |
| Continuous integration and error tracking | Planned | Tests currently run locally and in the release gate; see the [roadmap](docs/ROADMAP.md) |

## Documentation

| Document | What it covers |
| --- | --- |
| [Product overview](docs/PRODUCT_OVERVIEW.md) | Audiences, journeys, service model and product domains |
| [Feature catalogue](docs/FEATURES.md) | Every public and admin feature, grouped by area |
| [Architecture](docs/ARCHITECTURE.md) | Rendering model, request lifecycle, module boundaries and key design decisions |
| [Data model](docs/DATA_MODEL.md) | Models, shared sub-schemas, indexes and data rules |
| [Commerce and orders](docs/COMMERCE_AND_ORDERS.md) | Cart, checkout, order placement, rental rules and order lifecycle |
| [Content management](docs/CONTENT_MANAGEMENT.md) | Admin panel, document editor, media library, drafts, review and roles |
| [SEO and structured data](docs/SEO_AND_STRUCTURED_DATA.md) | Metadata, sitemap registry, robots policy, JSON-LD and URL continuity |
| [WordPress migration](docs/WORDPRESS_MIGRATION.md) | How the WooCommerce site was replaced without losing its URLs |
| [Localization](docs/LOCALIZATION.md) | One-URL language model, translation pipeline and safeguards |
| [Analytics](docs/ANALYTICS.md) | First-party visitor analytics and the GA4 integration |
| [Security architecture](docs/SECURITY_ARCHITECTURE.md) | Authentication, authorization, input handling and transport |
| [Deployment and operations](docs/DEPLOYMENT_AND_OPERATIONS.md) | Hosting topology, release gate, migrations, backups and verification |
| [Engineering quality](docs/ENGINEERING_QUALITY.md) | Test suite, release verification and engineering conventions |
| [Quality and discoverability](docs/QUALITY_AND_DISCOVERABILITY.md) | Accessibility, performance targets and publishing checks |
| [Project audit](docs/PROJECT_AUDIT.md) | Strengths, risk register, known limitations and recommendations |
| [Delivery timeline](docs/TIMELINE.md) | What was built when, from first commit to the November releases |
| [Roadmap](docs/ROADMAP.md) | Near-term and longer-term improvements |
| [Screenshot gallery](docs/SCREENSHOTS.md) | All 59 storefront and admin screenshots with captions |
| [Showcase boundary](docs/SHOWCASE_BOUNDARY.md) | What this repository includes and deliberately leaves out |

## Screenshot gallery

Storefront screenshots were taken from the live site on 26 Nov 2026. Admin screenshots come from a local production build connected to the development database: personal data and staff identities are blurred, and the counts are development values.

### Storefront

<table>
  <tr>
    <td width="33%"><img src="docs/screenshots/storefront/home-shop-by-category.jpg" alt="Shop by Category cards with product photos" /><br /><sub><b>Shop by category</b></sub></td>
    <td width="33%"><img src="docs/screenshots/storefront/home-rental-showcase.jpg" alt="Rental showcase with a 3D carousel of rental products" /><br /><sub><b>Rental showcase</b></sub></td>
    <td width="33%"><img src="docs/screenshots/storefront/home-promotion-countdown.jpg" alt="Promotion banner with a live countdown" /><br /><sub><b>Promotion with countdown</b></sub></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/storefront/shop-all-products.jpg" alt="Shop page grouped by category with a category sidebar" /><br /><sub><b>All products, grouped by category</b></sub></td>
    <td><img src="docs/screenshots/storefront/category-buying-guide.jpg" alt="Category buying guide with a table of contents" /><br /><sub><b>Buying guide with contents</b></sub></td>
    <td><img src="docs/screenshots/storefront/product-buy-rent-refill.jpg" alt="Product page with Buy, Rent and Refill tabs" /><br /><sub><b>Buy, rent or refill</b></sub></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/storefront/search-suggestions.jpg" alt="Header search showing live product and category suggestions" /><br /><sub><b>Live search suggestions</b></sub></td>
    <td><img src="docs/screenshots/storefront/cart-drawer.jpg" alt="Cart drawer with a rental item" /><br /><sub><b>Cart drawer</b></sub></td>
    <td><img src="docs/screenshots/storefront/home-ordering-steps.jpg" alt="Four-step ordering explanation" /><br /><sub><b>How ordering works</b></sub></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/storefront/blog-article.jpg" alt="Blog article with reading time and recent articles" /><br /><sub><b>Article page</b></sub></td>
    <td><img src="docs/screenshots/storefront/faq.jpg" alt="FAQ page with search" /><br /><sub><b>FAQ</b></sub></td>
    <td><img src="docs/screenshots/storefront/contact.jpg" alt="Contact page with call and WhatsApp actions" /><br /><sub><b>Contact</b></sub></td>
  </tr>
</table>

### Mobile

<table>
  <tr>
    <td width="25%"><img src="docs/screenshots/storefront/mobile-home.jpg" alt="Mobile home page with a sticky call, WhatsApp and location bar" /><br /><sub><b>Home</b></sub></td>
    <td width="25%"><img src="docs/screenshots/storefront/mobile-menu.jpg" alt="Mobile navigation drawer with Menu and Category tabs" /><br /><sub><b>Navigation drawer</b></sub></td>
    <td width="25%"><img src="docs/screenshots/storefront/mobile-category.jpg" alt="Mobile category page" /><br /><sub><b>Category</b></sub></td>
    <td width="25%"><img src="docs/screenshots/storefront/mobile-product.jpg" alt="Mobile product page" /><br /><sub><b>Product</b></sub></td>
  </tr>
</table>

### Admin panel

<table>
  <tr>
    <td width="33%"><img src="docs/screenshots/admin/dashboard.jpg" alt="Admin dashboard" /><br /><sub><b>Dashboard</b></sub></td>
    <td width="33%"><img src="docs/screenshots/admin/products-by-category.jpg" alt="Products grouped into category cards" /><br /><sub><b>Products by category</b></sub></td>
    <td width="33%"><img src="docs/screenshots/admin/category-content-studio.jpg" alt="Category content studio" /><br /><sub><b>Category studio</b></sub></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/admin/product-document-editor.jpg" alt="Product description in the document editor" /><br /><sub><b>Product description editor</b></sub></td>
    <td><img src="docs/screenshots/admin/seo-review-custom-schema.jpg" alt="Custom JSON-LD field showing the automatic schema" /><br /><sub><b>Custom schema (JSON-LD)</b></sub></td>
    <td><img src="docs/screenshots/admin/media-library.jpg" alt="Media library grid with file details" /><br /><sub><b>Media Library</b></sub></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/admin/orders.jpg" alt="Orders list with customer column blurred" /><br /><sub><b>Orders</b> (personal data blurred)</sub></td>
    <td><img src="docs/screenshots/admin/redirections.jpg" alt="Redirections manager" /><br /><sub><b>Redirections</b></sub></td>
    <td><img src="docs/screenshots/admin/seo-overview.jpg" alt="SEO overview" /><br /><sub><b>SEO overview</b></sub></td>
  </tr>
  <tr>
    <td><img src="docs/screenshots/admin/seo-content-audit.jpg" alt="Content audit table" /><br /><sub><b>Content audit</b></sub></td>
    <td><img src="docs/screenshots/admin/visitor-analytics.jpg" alt="Visitor analytics dashboard" /><br /><sub><b>Visitor analytics</b></sub></td>
    <td><img src="docs/screenshots/admin/project-audit.jpg" alt="Project audit overview" /><br /><sub><b>Project audit</b></sub></td>
  </tr>
</table>

## Public repository boundary

This repository holds documentation and screenshots only. It does not contain application source code, the package lockfile, environment files, credentials, database exports, customer or order records, server addresses, deployment scripts or the private runbooks. Module names, patterns and decisions are described so a developer can judge the work; the repository is not a template or a self-hosting kit. See [the showcase boundary](docs/SHOWCASE_BOUNDARY.md).

---

<div align="center">

Oxygen Cylinder BD · Dhaka, Bangladesh · [www.oxygencylinderbd.com](https://www.oxygencylinderbd.com/)

Documentation and screenshots © Oxygen Cylinder BD. All rights reserved. Product and company names belong to their respective owners.

</div>
