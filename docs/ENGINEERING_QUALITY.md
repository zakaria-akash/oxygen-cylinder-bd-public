# Engineering quality

> How correctness is checked before, during and after a release, and the conventions that keep a fast-moving codebase predictable. [Back to the overview](../README.md)

## Contents

- [Testing approach](#testing-approach)
- [The test suites](#the-test-suites)
- [Release-time verification](#release-time-verification)
- [Engineering conventions](#engineering-conventions)
- [Living documentation](#living-documentation)
- [Known gaps](#known-gaps)

## Testing approach

The automated suite uses **Node's built-in test runner**, with no third-party test framework. It runs **offline**: no network calls, no database, and no paid API.

| Principle | How it shows up |
| --- | --- |
| Test the rules, not the framework | Business rules (category ordering, legacy URL matching, redirect safety, media references, schema handling) live in pure modules that tests can import directly |
| Inject anything that costs money or touches the network | The translation engine receives its provider, cache and clock as dependencies, so tests use fakes and can't bill the translation API |
| Use production-shaped fixtures | The header-menu tests use a fixture shaped like the live catalogue, including categories whose slugs differ between development and production |
| Name tests after behaviour | For example: "unsafe, cross-domain, internal and looping redirects are rejected" and "retains media shared by a hidden product, a draft, or an order snapshot" |
| Encode real incidents | When a production incident teaches something, it becomes a test, for example the Search Console URL from 29 Oct 2026 in the legacy product-slug suite |

## The test suites

**103 tests in 15 suites**, all passing on the private repository's head commit (25 Nov 2026). There is also a headless-browser language test.

| Suite | Tests | Covers |
| --- | ---: | --- |
| Translation engine | 14 | Caching, de-duplication, failure handling, quota back-off, protected tokens, HTML safety |
| Orphan media cleanup | 11 | Recognizing every way content can reference a file; keeping files used by hidden products, drafts and order snapshots; deleting only after a confirmed removal |
| Translation catalogue | 9 | Allowlist generation, admin and public scopes, bounded caches, no credential or URL payloads |
| Custom schema | 9 | Automatic versus custom schema, accepted input forms, actionable errors, `<script>` escaping, the unchanged site-wide graph |
| Translation routing | 8 | One clean URL per page in both languages, preserved query strings and encoded slugs, private paths never localized |
| Product categories | 7 | Multi-category membership, positions per category, re-numbering when products move |
| Legacy product slugs | 7 | Location and price endings, the Search Console case, refusing ambiguous matches |
| Media metadata | 7 | Image details as a single global record, revisions, copying details into pages without overwriting page-only values |
| Legacy WordPress URLs | 6 | Former pages and plugin rules, archives, pagination, the old staff shortcut |
| Header menu | 6 | The approved menu plan, exact slugs over aliases, links surviving renames, fallbacks |
| Shop page | 6 | The locked default category, picks ordering, robustness to stale or duplicate picks |
| Admin problem guide | 5 | Error modals that name the field, explain status codes and always offer remedies |
| Category URL links | 3 | Rewriting saved links when a category URL changes, without touching drafts or other sites |
| Redirections | 3 | Normalized source keys, query matching modes, rejection of unsafe, cross-domain and looping rules |
| Product contents default | 2 | Product tables of contents are opt-in; category and blog defaults unchanged |

The **browser test** launches the installed Chrome over the DevTools protocol, switches languages on real pages and checks the result, with translation responses mocked.

## Release-time verification

Automated tests are one layer. Every production release also passes:

| Stage | Checks |
| --- | --- |
| Build gate | Fresh database backup, checksum, `npm ci`, a clean `npm audit`, the full test suite, a production build |
| Switch | About 26 HTTP smoke checks through the real web server, with automatic rollback on failure |
| After the switch | Every category page against the database, every product page, every header link, JSON-LD before and after, byte-for-byte baselines for pages that shouldn't change, `401` on new admin endpoints |

See [deployment and operations](DEPLOYMENT_AND_OPERATIONS.md) for details.

## Engineering conventions

| Convention | Why |
| --- | --- |
| **TypeScript strict mode**, with typed JavaScript modules allowed | Shared types across server, client and tests; older modules migrate gradually |
| **One module per rule**, imported by both the UI and the API | Role permissions, rental packages, order status changes and URL building can't disagree between screens and endpoints |
| **`server-only` on secret-handling modules** | The build fails if one is imported into browser code |
| **Deny by default** | New admin endpoints are closed to Content Admins until a handler opts in |
| **Dry-run-first scripts** | Every migration and repair script prints its plan first and needs `--apply` to write |
| **Revision checks on shared records** | Concurrent edits are detected rather than silently overwritten |
| **Generated artefacts are checked** | The translation catalogue and the public media manifest are regenerated by the `dev` and `build` steps, and the translation tests fail when the committed catalogue is stale |
| **LF line endings everywhere** (`.gitattributes`) | A release archive made on Windows matches the repository byte for byte; a CRLF archive once broke a sibling project's server build |
| **Conventional Commits** | Most of the 128 commits use `feat`, `fix`, `docs`, `chore`, `style`, `refactor` or `ops` prefixes, which keeps the history easy to scan |
| **Explain failures to people** | Every admin error message is mapped to a cause and a remedy, and that mapping is tested |

## Living documentation

The private repository keeps operational knowledge next to the code. Each of these documents is updated as decisions change:

| Document | Purpose |
| --- | --- |
| Hosting and data-migration runbook | Phases, gates, owner decisions, rollback references and a dated decision log of every production release |
| Server dashboard | The current state of the host and its services |
| Language translation plan | The localization design and a decision log for routing, provider and policy changes |
| SEO implementation runbook | How metadata, schema and sitemap rules are implemented and verified |
| Image metadata and page-sharing runbooks | How image details and social sharing values flow through the system |
| Site speed and clean-up plan | Measured performance work and planned clean-ups |

## Known gaps

| Gap | Plan |
| --- | --- |
| No CI service runs the tests on every push | Add a CI workflow with the same test command and a type check |
| Route handlers and database queries have no integration tests | Add tests against an isolated MongoDB instance for orders, redirects and media cleanup |
| No end-to-end test of the checkout | A headless browser test against a seeded environment |
| No automated accessibility or performance budget | Lighthouse and axe checks on key pages in CI |
| Error tracking is log-based | Add structured error reporting with alerting |

These are listed in the [roadmap](ROADMAP.md).
