# Oxygen Cylinder BD — Public Project Showcase

**A bilingual medical-equipment storefront and content platform for Bangladesh**

[Visit the live website](https://www.oxygencylinderbd.com/)

This repository documents the product, user experience, architecture, and engineering roadmap behind Oxygen Cylinder BD. The production application is maintained in a separate private repository. This showcase contains documentation only; it does not include application source, executable code, database exports, media uploads, credentials, or server configuration.

## Project at a glance

Oxygen Cylinder BD helps people and care teams discover medical equipment and contact the Dhaka-based team about purchase, rental, refill, delivery, and support. The site brings product discovery, service information, editorial guidance, and customer contact paths together in English and Bengali.

| Area | What visitors can do |
|---|---|
| Product discovery | Browse medical oxygen cylinders and concentrators, respiratory equipment, hospital beds, mobility products, diagnostics, and care supplies. |
| Purchase options | Review products for purchase, rental, or refill where offered, then contact the team to confirm availability and terms. |
| Product guidance | Compare product details, specifications, care information, and buying guidance. |
| Editorial content | Read articles and practical information about medical equipment and home care. |
| Customer support | Find contact details, FAQs, delivery information, and support options. |
| Language access | Browse the public experience in English or Bengali. |

## Documentation

- [Product overview and visitor journeys](docs/PRODUCT_OVERVIEW.md)
- [Feature catalogue](docs/FEATURES.md)
- [Architecture and operating principles](docs/ARCHITECTURE.md)
- [Discoverability, accessibility, and performance](docs/QUALITY_AND_DISCOVERABILITY.md)
- [Roadmap and improvement themes](docs/ROADMAP.md)
- [Public repository boundary](docs/SHOWCASE_BOUNDARY.md)

## Technology overview

The private production application is built with Next.js App Router and React. Published catalogue and editorial data are served from MongoDB using Mongoose; uploaded media are managed by the application. The website is designed for responsive use across mobile and desktop screens, with server-rendered public pages and interactive client-side features where needed.

This repository intentionally avoids publishing implementation details that could expose proprietary logic, credentials, private data, or production infrastructure.

## Project status

The public website is live. Product, category, informational, and editorial content is maintained through a private administration experience. Feature details in this showcase describe the intended public product and may evolve as the live service changes.

## Contact

For product availability or customer support, use the contact information on the [official website](https://www.oxygencylinderbd.com/).

---

© Oxygen Cylinder BD. Product and company names belong to their respective owners.
