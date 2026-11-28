# Showcase boundary

> What this public repository contains, what it deliberately leaves out, and the rules for adding to it. [Back to the overview](../README.md)

This repository explains the Oxygen Cylinder BD platform to customers, collaborators, reviewers and prospective clients. The production application lives in a separate private repository.

## Included

- Product, feature and visitor-journey descriptions.
- Architecture, data-model and security **design**, with real module names and patterns, as text and diagrams.
- Operational practice: the release gate, verification, migrations and backups, described without infrastructure details.
- Measured, dated figures (catalogue size, test counts, sitemap size, response times) and where they came from.
- Screenshots of the public site and of the admin panel, redacted as described below.
- An honest engineering review, roadmap and delivery timeline.

## Excluded

| Category | Examples |
| --- | --- |
| Source code | Application code, scripts, configuration, the lockfile, build output |
| Secrets | Credentials, tokens, keys, connection strings, environment files |
| Data | Database exports, customer and order records, chat and enquiry content, unpublished drafts, analytics records |
| Infrastructure | Server addresses, host names other than the public domain, account names, file paths, service and unit files, firewall and proxy rules, backup locations |
| Private documents | The hosting runbook, the server dashboard, internal plans |
| Security findings | Results of internal audits, unresolved weaknesses, anything that would help an attacker |
| People | Staff identities, customer identities, internal contact details |

## Screenshot policy

- **Storefront** screenshots come from the live public site. They were taken with analytics, the visitor beacon and chat requests blocked, so they don't distort the site's statistics, and without submitting anything.
- **Admin** screenshots come from a local production build connected to the development database, never from the live admin panel.
- In admin screenshots, the following are **blurred**: signed-in staff names and emails, customer details, chat and enquiry content, author bylines and network names. Business contact details that are already public on the website are shown as they appear there.
- Screens that expose internal security findings, or that carry stale or misleading information, are left out.

## Accuracy policy

- Figures are dated and attributed: "as shown on the live `/shop` page on 26 Nov 2026", "measured on the private repository at commit `7090601`".
- Features are described only after checking the code or the live site. Where something depends on configuration (for example, a translation provider or Google credentials), the documentation says so.
- Planned work is labelled as planned. The roadmap is a direction, not a promise.

## Contributing to this repository

Before adding material, confirm that it:

1. is suitable for public release and approved by the owner,
2. contains no source code, secrets, personal data, infrastructure details or security findings,
3. is accurate, and dated if it is a measurement,
4. uses redacted screenshots where staff or customer data could appear.

Keep the repository documentation-only.

## Rights

Documentation, screenshots, product names and the Oxygen Cylinder BD brand are © Oxygen Cylinder BD. All rights reserved; no licence is granted to reuse them. Third-party names and trademarks belong to their owners.
