# Roadmap

> Where the platform goes next. This is a direction, not a delivery promise; priorities follow the business and what measurement shows. [Back to the overview](../README.md)

## Near term

| Item | Why |
| --- | --- |
| Continuous integration | Run the type check and the 103-test suite on every push, not only at release time |
| Uptime monitoring and alerting | Catch problems between releases, not just during them |
| Off-server backup copies and a restore drill | Survive the loss of the whole host and prove restores still work |
| A dedicated translation provider key with quota monitoring | Make Bengali copy reliable; warm the cache after configuration |
| Field performance data | Access-log timing and mobile Core Web Vitals by page group (home, shop, category, product, blog) |
| Legacy URL review | Decide the remaining old WordPress addresses that the automatic rules deliberately leave as 404 |
| Retire the old WordPress install | After a final verified archive, at the owner's go-ahead |

## Next

| Item | Why |
| --- | --- |
| Integration tests against an isolated MongoDB | Orders, redirects, media cleanup and permissions, end to end through the route handlers |
| End-to-end browser tests | Checkout, admin sign-in and the main editors against seeded data |
| Structured error reporting | Faster diagnosis than reading logs |
| Lighter pages | Trim the shared legacy CSS, re-encode the heaviest media, bound "related items" queries, cache shared published data with invalidation |
| Accessibility audit | Keyboard and screen-reader passes over the main journeys, in both languages |
| Human-reviewed Bengali | Policies, top categories and key guides reviewed by a person rather than left to machine translation |
| Image title clean-up | Replace imported WordPress file names in image titles with descriptive titles |
| Security hardening | Continue strengthening admin sign-in and response headers, tracked in the private audit |

## Later

| Item | Why |
| --- | --- |
| Release automation | Build from a pinned commit through the same gate, with less manual packaging |
| Shared rate-limit store | Needed only before running more than one application instance |
| Stock and delivery-fee models | If the business wants inventory counts and automatic delivery quotes |
| Merchant product feed | Product listings in shopping surfaces, using the existing Merchant Center connection |
| Customer accounts or online payment | Only if customers ask for them; guest ordering with cash on delivery fits the market today |

## Ongoing

- Dependency and framework updates through the release gate.
- Catalogue accuracy: prices, rental packages, stock and delivery wording.
- Content governance: editorial and medical review of guides and product copy.
- Search Console monitoring and redirect hygiene after URL changes.

## Recently delivered

| Delivered | When |
| --- | --- |
| Custom JSON-LD in every SEO Review, with reset to automatic | 22 Nov 2026 |
| Protected All Products category for `/shop` with per-category picks | 22 Nov 2026 |
| Approved header menu plan with resilient links | 22 Nov 2026 |
| Load More, 8 products per category on `/shop`, square gallery | 18 Nov 2026 |
| One set of image details across the site | 17 Nov 2026 |
| Strict recovery of old WordPress product URLs | 11 Nov 2026 |
| Multi-category products, editable category URLs, Redirections | 9 Nov 2026 |
| First-party visitor analytics and the GA4 module | 8 Nov 2026 |
| Domain switch from WordPress to the new application | 2 Nov 2026 |

The full history is in the [delivery timeline](TIMELINE.md).
