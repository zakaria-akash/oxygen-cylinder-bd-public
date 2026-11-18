# Architecture and operating principles

This overview describes system responsibilities at a product level. It does not disclose source code, private endpoint contracts, server paths, network rules, database credentials, or deployment scripts.

## Application layers

| Layer | Responsibility |
|---|---|
| Public web experience | Server-rendered pages for the home page, catalogue, categories, products, articles, and informational content. Interactive controls are used for search, filtering, cart actions, language choice, and support. |
| Content administration | Protected staff workflows for maintaining approved public content, catalogue data, media metadata, and selected site operations. |
| Application data | MongoDB stores structured product, category, article, page, and operational records. The live VPS database and local development database are separate environments. |
| Media management | Uploaded media and descriptive metadata are managed through the application so public pages can use consistent image details. |
| Hosting and delivery | A production web application serves the public domain. Static assets and media use browser and edge caching where appropriate. |

## Data boundaries

- Published records drive public catalogue and content pages.
- Administrative changes are authorized and validated before persistence.
- Local development and production use separate databases and can contain different content.
- Production content is not synchronized from local development as part of a normal application release.
- The public showcase contains no database dump or production record.

## Rendering and discoverability

Public pages are rendered so core page content and metadata are available to browsers and crawlers. Dynamic catalogue features complement direct, addressable product and category pages. Search, canonical page identity, structured metadata, and sitemap behavior are treated as part of the public experience.

## Reliability principles

- Keep customer and staff operations behind their intended access controls.
- Keep live content changes independent from application code releases.
- Use scoped backups and release procedures for production changes.
- Validate public routes and key purchase/contact journeys after a release.
- Prefer incremental changes that preserve editorial data and permit rollback.
