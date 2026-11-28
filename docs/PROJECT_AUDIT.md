# Engineering review

> An honest assessment of the private application: what is strong, where the risks are, what went wrong along the way, and what should come next. [Back to the overview](../README.md)

## Scope

This review is based on the private repository at commit `7090601` (25 Nov 2026), its test suite, its deployment log and read-only checks of the live site on 26 Nov 2026. It is not a penetration test, a load test or a compliance certification. Security findings that could help an attacker are deliberately left out of this public version.

## Inventory

| Dimension | Observation |
| --- | --- |
| Framework | Next.js 16.4.6, React 19.1, TypeScript 5.8 in strict mode |
| Size | 452 TS/JS files (≈ 54,000 lines), 118 components, 118 library modules, 21 models, 96 route handlers, 63 pages, 164 routes in the production build |
| Data | MongoDB 8 in production, MongoDB Atlas in development, GridFS for media |
| Audiences | Public visitors (guest ordering) and three staff roles |
| Integrations | Google Cloud Translation, GA4 (service account), Search Console and Merchant Center (OAuth), PageSpeed Insights, SMTP |
| Tests | 103 automated tests in 15 suites, plus a headless-browser language test and a release-time smoke suite |
| Hosting | Self-managed VPS behind Nginx and Cloudflare; systemd service; nightly checksummed backups |

## Strengths

| Strength | Evidence |
| --- | --- |
| Server authority over money and URLs | Orders are rebuilt from the catalogue; one URL builder; slug histories; a fail-open redirect layer |
| URL continuity through a platform migration | Kept WooCommerce URL shapes; a layered legacy resolver; a strict, tested matcher that recovered about 180 old product URLs |
| A safe content model | Typed blocks and text ranges instead of stored HTML; an allowlist paste parser; escaped JSON-LD |
| Consistency by construction | Role rules, rental packages, order status changes and indexability are each defined once and shared by the UI and the API |
| SEO as part of the runtime | One indexable-page registry for the sitemap, robots rules, inventory and audit; JSON-LD on every page with a validated per-page override |
| Privacy-aware analytics | No cookies, no stored IPs, daily-salted hashes, automatic expiry, visible bot filtering |
| Operational discipline | Gated releases with automatic rollback; dry-run-first migrations; post-switch checks that prove what changed and what didn't |
| Staff experience | Error modals that explain causes and remedies; editors that mirror the public pages; one set of details per image |

## Risk register

| Area | Current position | Recommended control |
| --- | --- | --- |
| Single host | The application and its database share one VPS; nightly backups are kept on that host | Copy backups off the server automatically and rehearse restores on a schedule |
| No continuous integration | Tests run locally and in the release gate, not on every push | A CI workflow running the type check and the test suite on every push and pull request |
| Integration coverage | Pure modules are well tested; route handlers and database queries are not | Integration tests against an isolated MongoDB for orders, redirects, media cleanup and permissions |
| End-to-end coverage | Checkout and admin flows are checked by people and smoke tests | Headless browser tests for checkout, sign-in and the main editors against seeded data |
| Translation dependency | Bengali copy depends on a translation provider and its quota; without it pages fall back to English | A dedicated provider key with quota monitoring, a warmed cache, and human-reviewed Bengali for key pages |
| Observability | Logs and release checks; no external uptime monitoring or error tracking | Uptime checks with alerting, structured error reporting, access-log timing |
| Rate limiting | In-process limits suit a single application instance | A shared store for limits before running more than one instance |
| Content governance | Editorial and medical review states exist; reviewer roles and cadence are informal | Named reviewers and a review schedule for medical and pricing copy |
| Dev/production divergence | The content team edits production daily; development is refreshed on request | Keep the guarded mirror script, and inspect production read-only before data-dependent releases |

## Incidents and lessons

Problems found along the way, and what changed because of them:

| What happened | Cause | Change |
| --- | --- | --- |
| Media detail saves failed in production but worked in development | A save used a multi-document transaction; the production database is standalone | `withOptionalTransaction()`, and a rule that new code must use it |
| A good release rolled itself back (twice) | Smoke tests used sample URLs the content team later renamed | Failed checks are re-run against the previous release before the code is blamed; samples are updated |
| A release build stopped at the test gate | A generated translation catalogue was committed stale | Run the tests locally on the exact commit before every deploy |
| A strict alias rule would have blocked real category saves | Production had categories whose URLs were retired aliases of other categories | Inspect production read-only before shipping data-dependent features |
| Search Console reported 5xx errors after the switch | They came from the old WordPress site before the switch; the real gap was old product slugs that a WordPress plugin used to hide | The strict legacy product matcher and a reviewed list for the rest |
| Category pages listed some products in the wrong order | "Also listed" products were sorted by their main category's position | One comparator shared by the admin list and the public pages; all 35 categories verified after the fix |
| A sibling project's release failed to build on the server (lesson adopted here before launch) | The release archive, created on Windows, had CRLF line endings | LF everywhere through `.gitattributes`, and line endings normalized in the build gate |

## Known limitations

- **No customer accounts or online payment.** This is by design for this market: orders are confirmed in conversation and paid on delivery.
- **No stock reservation.** "In stock" is a flag maintained by staff, not an inventory count.
- **Delivery fees are quoted by people.** There is no automatic calculation.
- **Bengali content depends on the translation provider.** Without one, visitors see English text in Bengali typography.
- **Imported image titles** still carry many original WordPress file names; a clean-up pass is pending.
- **This repository can't be run.** Application source, configuration and data are private.

## Recommendations

| Horizon | Recommendation |
| --- | --- |
| Now | CI on every push; external uptime monitoring; automatic off-server backup copies |
| Next | Integration and end-to-end tests; structured error reporting; field Core Web Vitals by page group |
| Then | Human-reviewed Bengali for key pages; trimming the shared CSS and heavy media; scheduled restore drills |
| Later | A stock and delivery-fee model if the business wants them; online payment only if customers ask for it |

## Conclusion

The project goes well beyond a brochure site. It is a migrated, self-hosted commerce and content platform with a deliberate URL strategy, a safe content model, its own analytics and a release process that has proved itself in production. The largest remaining gains are in automation (CI, monitoring, off-server backups) and test depth (integration and end-to-end), not in the architecture.
