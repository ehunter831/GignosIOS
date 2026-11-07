# Gignos native iOS app — requirements for Eli

Prepared September 27, 2026 • Revised November 7, 2026 • Scope and acceptance instructions

## Assignment

Eli, build, publish, and maintain a polished native Gignos app for iPhone and iPad. People must be able to complete the agreed public browsing and research journeys inside the app. The highest priority is closing the gaps between the website's content and functionality and the native experience.

**The app must never send a user to the Gignos website to continue or finish an app task.** Missing content, unsupported media, errors, and incomplete features must not produce a website handoff. A website displayed inside a browser sheet or full-page web view does not satisfy native parity.

Use [Gignos-feature-priorities.md](Gignos-feature-priorities.md) for build sequencing. This document defines acceptance. These revised instructions replace the earlier allowances for secondary web destinations, “open original page” actions, and website fallbacks for media.

You own design, implementation, performance, automated and manual testing, TestFlight, App Store submission through your Apple Developer account, review fixes, release, and ongoing maintenance. Performance thresholds and maintenance response times below remain proposed targets for agreement; they are not measured results or commitments already accepted by Eli. No release date, price, or maintenance term has been agreed.

## 1. Reference material and current baseline

- Product reference: [Gignos website](https://gignos.com). Audit its public content and journeys, including secondary pages.
- Working repository: [ehunter831/GignosIOS](https://github.com/ehunter831/GignosIOS). This supersedes the original instruction to use a separate Gignos team repository. Keep implementation, tests, documentation, and release assets here.
- Read `README.md`, `docs/API.md`, and `docs/DESIGN.md`, but reconcile outdated statements with the code. The supplied SwiftUI ZIP is a historical reference.
- The app uses the public API at `https://gignos.com/research/api/v1`. Keep content and data relationships consistent with the production service.
- This revision reviews source at [commit 89649d4](https://github.com/ehunter831/GignosIOS/tree/89649d47964c8febf6f19f69a8677442c99617be), the current `main` when reviewed. It is a source review, not a fresh device test or production certification. Recheck this baseline when implementation begins.

### Preserve the work already present

The app has five peer catalogs, native record details and related-record navigation, text search, type/country filters, name/country sorting, a map/list flow, sharing, and persistent saved records. GRDB provides a SQLite catalog/detail cache and full-text search. Loading and refresh-failure states exist. Cache tests, an app icon, test-target signing, and an Xcode Cloud manifest are present.

Do not rebuild these merely because the original sample brief predates them. Extend them and verify complete journeys.

### Eli's focus areas

Every item below is a version 1 requirement. “Verify” means a capability exists in code but still needs acceptance evidence; it does not claim the feature is absent.

| # | Focus area | Current gap or verification need | Completion criteria |
| --- | --- | --- | --- |
| 1 | Keep every Gignos journey native | Catalog/detail/footer/menu/About links still open Gignos web destinations | All internal links resolve natively; no browser fallback, including errors (§2.1) |
| 2 | Native Home and discovery | Sites opens directly on the catalog; no Home discovery screen | Introduction, search, map, recent sites, and learning/research entries work in-app (§2.2) |
| 3 | Photos and galleries | Rows use system icons; details have no catalog photos; media fields are not modeled | Thumbnails, hero images, galleries, captions/credits, caching, and failure states (§2.3) |
| 4 | Complete search, filters, and sorting | Only type/country filters and name/country sorting; missing filter data in the model/index | Website-equivalent queries and complete results across all five catalogs (§2.4) |
| 5 | Rich details, citations, and relationships | Description HTML becomes plain text; extracted links use generic URL opening | Readable structured content, contextual citations, and complete native research journeys (§2.5) |
| 6 | Video, audio, and embedded media | No media viewer or media model in the reviewed detail implementation | Required media works in-app; failures preserve the record and never open the website (§2.6) |
| 7 | Secondary information and contact | About, News, Contact, Q&A, and Privacy are website links | Native destinations and any agreed public forms, with loading/error states (§2.7) |
| 8 | Classification hierarchy | Classifications use a flat catalog | Parent/child browsing, taxonomic filters, native record and related-site navigation (§2.8) |
| 9 | Universal Links | Associated-domain entitlement exists; no incoming router in app/root views; build IDs differ | Verified website association and routing to the correct native screen (§2.9) |
| 10 | Complete, reliable native journeys | Map, saved records, and cache exist; device/performance/accessibility acceptance remains | Consistent map/list state, offline text, preserved navigation, and release evidence (§2.10, §§3–8) |

## 2. Define and deliver native parity

Before implementation, produce a parity checklist with: website URL/flow, current app behavior, required native destination, data dependency, acceptance test, owner, and status. Use the numbered focus areas above. Include secondary pages, every internal link entry point, and media types. Record missing APIs and content as dependencies instead of hiding them behind web links.

Review the checklist with Derek. Any scope exclusion must identify the omitted task and its user impact; it cannot authorize sending the user to the website. Required items remain release blockers until implemented or explicitly removed from scope. Document known website bugs and resolve their intended behavior rather than reproducing them.

### 2.1 Keep every Gignos journey native — first priority

**Current gaps to remove:**

- `CatalogView.swift`: “View on Gignos.”
- `RecordDetailView.swift`: “Full entry on Gignos” and the footer directing people to the website for media/formatting.
- `WebsiteMenu.swift`: web links for Home, About, News, Contact, Q&A, and Privacy Policy.
- `RootView.swift` / `AboutView`: “Visit Gignos” and “About the project.”
- `RecordDetailView.swift` / `GignosAPI.swift`: references extracted from descriptions are passed to generic `Link` controls, including links that may lead to Gignos records or citation anchors.

**Required behavior:**

- Implement a shared internal route resolver. Internal buttons, record links, relative URLs, citation links, Home cards, menus, and incoming links must select a native destination directly.
- Do not open a Gignos URL through Safari, the default browser, `SFSafariViewController`, or a full-page `WKWebView` as a substitute for a native screen. Do not open a URL externally hoping Universal Links will bring it back into the app.
- Preserve the user's tab, back path, scroll position, and search/filter context. Close a gallery, player, or sheet back to the originating record.
- Missing records, unsupported routes, offline content, server errors, and media failures must show an appropriate native explanation with retry/back/search recovery. Never add “Continue on website,” “Open on website,” or an automatic redirect.
- Fetching API data, images, or media over HTTPS is expected and is not a website handoff. Limited embedded media rendering is allowed under §2.6; embedding the Gignos website is not.
- Sharing or copying a canonical HTTPS URL is allowed and must not navigate the originating app away. A user explicitly sharing to another app is distinct from Gignos redirecting them.
- Third-party publications and original sources are reference material. Present the citation natively and use an explicitly selected in-app reference viewer where appropriate. Never automatically launch an external site or use a third-party page to replace a missing Gignos feature. User-initiated system share and email composition remain allowed.

**Acceptance:** Walk every menu, catalog, detail, rich-text link, Home card, saved record, map selection, and media/error state. No first-party action opens a website. Add routing/UI regression coverage for the existing escape points above, unknown internal URLs, and blocked/failed content. An unavailable placeholder is a recovery state, not evidence that a required feature is complete.

### 2.2 Native Home and discovery

**Current gap:** `RootView` puts `CatalogView` at the root of each tab. The app has no native equivalent of the [website Home](https://gignos.com) discovery experience.

Preserve Sites, Classifications, Time periods, Cultures, and Sources as five peer sections in that order. Proposed Home placement is the opening page inside Sites, with the full site catalog one level below; confirm placement at the first milestone.

Build a native introduction, search entry, map entry, recently added site cards with photos, and learning/research entries. “Learn about human origins” must enter native exploration. “Research opportunities” must lead to native information/contact; it must not become a website exit. Include any required Home media through the same in-app media components used on records.

Confirm how the website chooses and orders “recently added” with the backend owner. Do not assume `updatedAt` means creation date. Specify the source of Home copy and secondary content; request an endpoint or maintained content source where none exists.

**Acceptance:** From a fresh launch, a user can understand Gignos, search, open the map, explore a recent site, and read learning/research information without leaving the app. Verify empty, loading, cached, and refresh-failure states.

### 2.3 Photos, hero images, and galleries

**Current gap:** `RecordRow` displays system symbols and `RecordDetailView` does not render catalog images. The current `CatalogRecord` and API decoder do not retain the image/media data needed for parity.

Add thumbnails to records with images, a hero image on applicable details, and a swipeable full-screen gallery with zoom and dismissal. Include captions, source attribution, and credits where supplied. Previously identified API fields include `heroMediaPreview`, `heroMediaSourceNames`, and `relatedObjects.medias[].url`; verify the current payload and rights before implementation.

Extend the model, decoding, persistence, and cache migrations as well as the views. Load lazily, size images to the display, and cache appropriately. Missing or failed images need stable placeholders and must not block text or navigation. Do not invent photos or attribution.

**Acceptance:** Verify a record with a hero and multiple images, one without images, a failed image request, and a cached/offline revisit. Confirm full-screen viewing, captions/credits, accessible descriptions where available, and responsive scrolling.

### 2.4 Complete catalogs, search, filters, and sorting

**Current gap:** `CatalogFilter` and the SQLite query support only text, site type, country, and name/country ordering. The current model/index does not retain all relationship and numeric date fields needed for additional filters. This is data/model/query work as well as UI work.

All five catalogs must include the same public records and relevant fields as the website. Search and filters must cover the full relevant dataset, including records outside the first downloaded page.

For Sites, implement name/search, type, function, classification, country, time period, and age-range controls, plus age sorting. Verify behavior against [Sites](https://gignos.com/sites). For Classifications, implement the applicable taxonomic filters. Audit the remaining catalogs for their applicable search/sort/filter behavior.

Confirm and document combination semantics, age-range overlap/boundaries, BP units, unknown-date handling, reset behavior, result counts, and sort tie-breaking with the product/backend owners. Retain typed dates and relationships needed for queries; do not filter formatted display strings or guess enum meanings. Preserve appropriate data/index migrations.

**Acceptance:** Compare record identities and counts for representative single and combined filters. Include exact age boundaries, unknown dates, reset/no-results cases, and records beyond the first page. Verify the map receives the same filtered results. A partially downloaded cache must be labeled partial; it must never present an incomplete search/count as complete.

### 2.5 Rich details, citations, and related-record journeys

**Current gap:** `GignosAPI` converts descriptions to plain text and extracts links into a separate collection. Details display `Text` and generic links. Basic related-record navigation exists, but that does not establish parity for structured descriptions, inline references, or all relationship types.

Preserve meaningful headings, paragraphs, lists, emphasis, scientific names, reference markers, and required structured content. Render supported content using native text/layout. If an existing public table or other content is required for a task, design an in-app presentation; do not assume dense content is automatically excluded.

Show available descriptions, metadata, dates, provenance, citations, and source authors. Preserve Wikipedia-derived/unreviewed notices. Tapping a citation must open the relevant native source or reference location while retaining a clear return path. Internal links embedded in descriptions must use §2.1 routing, including relative links and supported fragments.

Verify site → classification/time period/culture → related site and site → cited source → related record journeys. Preserve numeric source IDs; do not assume every record uses a slug. Missing fields or broken references need honest, useful states, without invented data.

**Acceptance:** Compare a rich record such as Laetoli Site G and representative records from each of the other four catalogs with the website. Check structured text, reference order, source authors, inline citation actions, cross-catalog navigation, and return to the original results with filters/position preserved.

### 2.6 Video, audio, and embedded media

**Current gap:** The reviewed detail model/decoder does not retain media collections and the detail view has no video, audio, or model viewer. The prior “Open on website” fallback is removed.

Inventory actual image, video, audio/podcast, and 3D/embed formats across the catalog. Record representative URLs, API fields, provider restrictions, captions/credits, rights, required viewer, and acceptance status. Previously identified fields include `medias[].fileType`, `embedLinkCode`, and `sourceUrl`; confirm their contract.

Use native playback where supported. A contained provider embed may render an individual media item when required, but must stay within the app, retain native dismissal/navigation, and prevent unsolicited external navigation. Do not load an entire Gignos page as the player or open YouTube/Safari automatically.

For unsupported, unavailable, or offline media, keep the record readable and explain the problem in-app with retry where useful. If required media cannot be presented, record the dependency and resolve it before acceptance; an unavailable message does not close the parity item. Basic viewing of required 3D content belongs to parity. Optional placement of a model in a room with AR can follow in a later release.

**Acceptance:** Test each required format/provider, start/pause/full-screen/dismiss controls as applicable, an unavailable embed, network interruption, and offline access. Media failures never send the user to the website or erase their record/navigation context.

### 2.7 Native secondary information and contact

**Current gap:** `WebsiteMenu` routes Home, About, News, Contact, Q&A, and Privacy to web pages. `AboutView` also contains website exits.

Provide native About, News list/article, Contact, Q&A, and Privacy destinations with the relevant public content. Replace website menu links with native navigation. Provide native research-opportunity information and a clear contact action. A user-initiated system email composer is acceptable; do not redirect to a web contact page.

Inventory public contact/newsletter/submission flows. Where agreed in scope, provide native fields, validation, submission progress, success/failure feedback, and the necessary backend integration. Do not introduce accounts or editorial tools without a scope decision. If a flow is excluded, document that exclusion and omit its action; do not advertise an app feature that sends users to the website.

**Acceptance:** Every secondary menu item and Home research entry opens its native destination and returns correctly. Confirm content ownership/update mechanism and loading/offline/error states. Any included form must complete in-app.

### 2.8 Classification hierarchy

**Current gap:** Classifications are displayed through the same flat catalog as other sections. The current record model does not retain ancestry for tree navigation.

Use the supplied `ancestry`, rank, and parent/child relationships to support hierarchical browsing (for example, genus → species), alongside search and applicable taxonomic filters. Confirm the backend's ancestry format and unknown/missing-parent behavior. Each node must open its native record and related sites. Do not infer taxonomy from names or fabricate missing relationships.

**Acceptance:** Browse multiple levels, open a node, explore a related site, and return to the previous hierarchy position. Verify root nodes, missing ancestry, and search/filter results.

### 2.9 Universal Links — bring users into the app

**What they are:** A Universal Link is an ordinary HTTPS URL, such as a canonical Gignos record link, that iOS can associate with the installed app. A supported link tapped in Messages, Mail, or Notes can open the corresponding native screen directly. Without the app, the same URL remains usable in the browser. This is an incoming entry point; it does not require the app to open a website. See Apple's [Universal Links overview](https://developer.apple.com/documentation/xcode/allowing-apps-and-websites-to-link-to-your-content).

**Three different behaviors:**

| Situation | Required outcome |
| --- | --- |
| A user taps an internal Gignos link while using the app | Navigate directly to the native screen; no browser and no attempt to leave/reopen the app |
| A supported Gignos HTTPS link is opened from another app, with app opening enabled by iOS/user choice | Open the matching native tab/detail |
| A person without the app opens the shared URL, or deliberately chooses browser browsing outside the app | The website remains available; the Gignos app did not initiate a handoff |

Universal Links are also separate from Apple's cross-device Handoff feature. App-to-browser Handoff is not part of this scope.

**Current gap:** `Gignos.entitlements` already contains `applinks:gignos.com`, and the project references the entitlement file. That is only part of the implementation: `GignosApp` and `RootView` have no incoming URL handler/router. The app target uses `com.gignos.ios` in Debug and `com.ggignos.ios` in Release. Confirm and correct or deliberately configure the intended identities before publishing the association. Production AASA deployment and device behavior have not been verified in this review.

**Deliver these three pieces:**

1. **App identity and entitlement — Eli.** Enable Associated Domains for the actual production app and verify the entitlement in the signed release build. Resolve the Debug/Release bundle-ID discrepancy. Use the signed `application-identifier` when coordinating the association; do not guess it from a sample configuration. Apple's [association troubleshooting](https://developer.apple.com/library/archive/qa/qa1916/) explains this identity check.
2. **Website association — website owner, coordinated by Eli.** Serve an `apple-app-site-association` (AASA) JSON file, without a filename extension, at `https://gignos.com/.well-known/apple-app-site-association`. Its `applinks` entry identifies the signed app and the URL patterns it handles. Deliver it publicly over HTTPS without redirects. Configure any additional supported host, such as `www`, explicitly rather than assuming the root domain covers it. See [Supporting associated domains](https://developer.apple.com/documentation/xcode/supporting-associated-domains).
3. **Native routing — Eli.** Receive incoming URLs in the SwiftUI app and send them through the same resolver used for internal navigation. Validate scheme, host, path, identifier, and supported parameters/fragments. Select the correct tab, fetch a requested detail directly when uncached, and support both cold launch and an already-running app. Do not require every catalog to finish downloading before opening one record. See [Supporting Universal Links in your app](https://developer.apple.com/documentation/xcode/supporting-universal-links-in-your-app).

**Route matrix to deliver:** These are path patterns, not invented record URLs. Confirm actual public examples, aliases, parameters, and fragments during discovery.

| URL path | Native destination |
| --- | --- |
| `/` | Agreed Home screen |
| `/sites`, `/classifications`, `/time_periods`, `/cultures`, `/sources` | Matching catalog/tab |
| `/sites/<slug>`, `/classifications/<slug>`, `/time_periods/<slug>`, `/cultures/<slug>` | Matching native record |
| `/sources/<numeric-id>` | Native source record using its numeric ID |
| `/about`, `/news`, `/contact`, `/qanda`, `/privacy` and required article routes | Corresponding native information screen |
| Supported query/fragment variations | Documented native filter state or content location |

Exclude API, administrative, and genuinely out-of-scope paths from the AASA rules. If the app encounters an unsupported internal route anyway, show native recovery; do not open the website. Missing records and unavailable networks follow the same rule.

**Acceptance:** On physical iPhone and iPad, test actual links from Notes/Messages with the app terminated and running. Cover all five catalogs, representative details, numeric source IDs, Home/secondary pages, parameters/fragments, malformed links, missing records, and cached/uncached offline cases. Verify destination, back navigation, and no browser bounce. Separately test the app-absent/browser-choice behavior.

Same-domain Safari navigation can remain in Safari; typing a URL into the address bar is not an app-opening test. Respect the user's outside-app browser choice. Account for Apple's AASA CDN caching when diagnosing changes. Follow [TN3155: Debugging Universal Links](https://developer.apple.com/documentation/technotes/tn3155-debugging-universal-links).

Deliver the route matrix, production AASA file, signed entitlement/identity evidence, routing tests, and device results. Start account/domain coordination immediately alongside the first parity work. Domain work is a release dependency, not a reason to add a website fallback.

### 2.10 Map, saved records, offline behavior, and navigation

**Existing foundation to verify:** `AtlasView` uses the catalog's filtered results and opens native details. Saved records and SQLite catalog/detail caches exist. Preserve these while adding the missing content and routes.

- Keep map/list filters and counts consistent. Missing coordinates must not create fabricated pins. Preserve the website's intentionally limited location precision; see the [Sites location policy](https://gignos.com/sites). The non-map path must reach the same records.
- Preserve tab state, search, filters, sorting, and scroll position when returning from details, media, sources, or another tab.
- Save/remove records across all five catalogs. Saved text must survive termination and upgrades and remain readable offline. Clearly distinguish saved/cached content from content that still needs a connection.
- Keep usable cached data when refresh fails. Present distinct loading, partial-data, empty, no-results, offline, and failure states with useful recovery.
- Extend persistence and migrations for images/media metadata, typed filters, rich text, and relationships. Do not lose bookmarks or require a clean install.
- Full offline video/media downloads and cross-device sync remain outside the assumed version 1 scope. Their absence must not trigger a website exit.

**Acceptance:** Complete Home → filtered list → map → record → related source → back → save → terminate → offline reopen journeys. Test cold/no-cache and warm/cached states, failed refresh, interrupted network, and an upgrade with saved records. Attach evidence to the parity checklist.

## 3. Native design and accessibility

Use Swift and SwiftUI and follow Apple's [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/). Match Gignos identity while using familiar platform controls. Support iPhone and iPad layouts, light/dark appearance, larger text, VoiceOver, adequate contrast, reduced motion, rotation, and supported iPad resizing. Confirm the minimum OS and physical-device matrix at kickoff; use iOS 18+ as the starting proposal.

Acceptance includes readable content at the largest supported text sizes, correctly labeled controls, sensible VoiceOver focus order, comfortable touch targets, no clipped tab labels, no broken keyboard interactions, and a non-map path to equivalent information. Review complete user journeys on real devices, not only screenshots or simulators.

## 4. Performance requirements

The current app has a persistent SQLite catalog/detail cache and full-text search. Catalog synchronization still needs measurement on first install and as the dataset grows. Extend and measure the current implementation; the earlier statement that catalogs are only held in memory no longer describes the working repository.

Render useful content incrementally, persist appropriate catalog/detail caches, and refresh in the background. Do not require a full catalog download before showing a usable first page. Search across the full dataset must remain correct: use confirmed server-side search or a complete local index with clear readiness status. Never disguise searching a partial download as complete search.

Use bounded concurrency, cancellation, timeouts, deduplicated requests, image sizing/caching, and lazy media loading. Avoid fetching unrelated tabs or large media during launch. Preserve previously usable data when refresh fails. Coordinate necessary API changes instead of relying on undocumented query parameters.

### Proposed measurable targets

Measure release builds on a named older supported physical iPhone and a current iPhone. Use at least 30 runs per timing scenario and report the 95th percentile—the time within which 95% of runs finish. Record device, OS, dataset, cache state, network profile, and backend latency.

| Scenario | Target |
| --- | --- |
| Cold launch to responsive navigation | ≤ 2 seconds |
| First install, no cache: first usable catalog records | ≤ 3 seconds on a controlled 20 Mbps / 50 ms latency connection |
| Relaunch with cached data: useful content | ≤ 1 second |
| Open cached detail or switch to a loaded tab | ≤ 300 ms |
| Local search/filter results | ≤ 300 ms after input debounce |
| Uncached detail or server search | ≤ 2 seconds on the same controlled connection |

Also test at 1 Mbps / 300 ms latency, offline launch, interrupted connections, server errors, and a dataset at least ten times the observed current catalog size. Cached content must remain usable; uncached failures need a bounded wait and retry path. Establish and document a request timeout budget, proposed maximum 15 seconds per visible attempt.

Profile scrolling, memory, CPU, network traffic, and battery impact with Instruments. Address visible scrolling hitches and unbounded memory growth. Report end-to-end latency, including backend delay; identify backend blockers and agree a remedy or revised target rather than excluding that delay from results.

## 5. Engineering and release quality

Eli's working repository, `ehunter831/GignosIOS`, is the source of truth for implementation, tests, documentation, release assets, and maintenance changes; the accompanying ZIP is a historical reference. Work through branches and pull requests there. Use reviewed changes, reproducible builds, versioned releases, and continuous integration for builds and deterministic tests. Keep networking, data persistence, and presentation separable enough to test and maintain.

Automate tests for API parsing, pagination completeness and stable ordering, full-dataset search/filter logic, missing data, linked records, citations, cache refresh/failure, saved-record persistence and migration, cancellation, and malformed/failed responses. Add UI tests for the critical journeys in the parity checklist. Keep deterministic tests separate from read-only live API smoke tests.

The observed API has source records with numeric detail IDs, other records with slugs, mixed string/numeric page values, and source pagination that required explicit ID ordering to avoid duplicates. Preserve regression coverage for these cases and reconcile the adapter with the official backend contract. Backend repository access was unavailable during prototype work and must be arranged for production discovery.

Before release, complete device/OS, accessibility, offline, slow-network, interrupted-task, large-content, deep-link, fresh-install, and upgrade testing. Run a proposed minimum seven-day TestFlight round with Derek and representative testers. Re-test fixes and attach evidence to each release candidate.

Release gate: every required parity item passes, including zero Gignos website handoffs and verified Universal Links; agreed performance targets pass; no known crash, data loss, security-critical issue, blocked core journey, or high-severity defect remains. Minor issues require documented impact, an owner, a target fix date, and Derek's acceptance. “No bugs” means a rigorous release gate and ongoing defect response, not a promise that no future defect can exist.

## 6. App Store publication through Eli's account

Eli will use and maintain his own Apple Developer Program account, configure production signing and the app identifier, manage TestFlight and App Store Connect, prepare the icon/screenshots/description/support and privacy links, complete applicable disclosures and ratings, submit the app, respond to review, and resolve rejection issues through publication.

Audit included SDKs, permissions, media/content rights, and actual data collection so App Store declarations match the shipped app. Keep public browsing usable without a new login unless an approved requirement needs one. Check current submission requirements at release time. Apple's [review guidelines](https://developer.apple.com/app-store/review/guidelines/) require release-ready testing, accurate metadata, and accessible backend services; Apple controls approval and timing.

Confirm the account type and public seller identity before creating the listing. With individual enrollment, Eli's legal name is the seller; see [Apple's enrollment guidance](https://developer.apple.com/help/account/membership/enrolling-in-the-app). This follows the requested publishing arrangement.

Document code/content ownership, authorization to publish Gignos, repository access, App Store Connect access where supported, responsibility for recurring fees, and the process for a future maintainer or account transfer. App transfers depend on Apple's eligibility criteria and account-holder actions; see [Apple's transfer process](https://developer.apple.com/help/app-store-connect/transfer-an-app/overview-of-app-transfer). Do not leave release assets or operating instructions available only on one developer's computer.

## 7. Maintenance responsibility

Eli owns ongoing mobile-app maintenance after launch: monitor crashes and performance, triage user reports, fix regressions, maintain OS/device compatibility, respond to API changes, update dependencies and store disclosures, renew necessary account/signing resources, and publish maintenance releases.

Proposed service expectations, to agree with scope and fees:

| Priority | Examples | Response and action target |
| --- | --- | --- |
| Critical | Widespread launch failure, data loss, serious security issue | Acknowledge within 1 business day; mitigation or hotfix submission target within 2 business days |
| High | Core feature unusable for a meaningful group of users | Acknowledge within 2 business days; fix or dated recovery plan within 5 business days |
| Normal | Minor defects and polish | Triage weekly; schedule in the next agreed maintenance release |

These are business-day targets, not a 24/7 commitment. Apple review time is outside the hotfix submission target. Define coverage hours/time zone and escalation contact before launch.

Review crash/performance signals each business day during the first two weeks, then weekly. Provide a monthly summary of app health, incidents, fixes, compatibility work, and upcoming risks. Test major iOS changes before public release where practical. Keep crash/performance monitoring proportionate and reflected in privacy disclosures.

Agree an initial maintenance term, included monthly effort or retainer, bug-fix versus new-feature boundaries, and renewal/exit arrangements before development starts. Backend operations remain with the existing backend owner unless separately assigned; Eli must diagnose and coordinate backend-dependent mobile issues through resolution. On handoff, deliver current source, build/release instructions, assets, open issues, and account-transfer assistance where eligible.

## 8. Milestones and definition of done

1. **Discovery:** approved parity checklist, Home/navigation design, API/dependency assessment, device matrix, performance plan, estimate, timeline, and maintenance terms.
2. **Functional beta:** all agreed journeys implemented using live data; automated tests pass; initial performance measurements provided.
3. **Release candidate:** TestFlight feedback addressed; physical-device/accessibility/network/upgrade tests complete; performance and defect gates met; Derek accepts the release candidate.
4. **Publication:** app approved and publicly available under Eli's developer account; production smoke tests pass; store listing and release documentation delivered. Submission alone does not complete this milestone.
5. **Maintenance:** agreed monitoring, defect handling, compatibility updates, and reporting are operating for the agreed term.

Deliver the repository and reproducible project, itemized parity checklist with acceptance evidence, content/media inventory, URL route matrix, production AASA and signed-app identity evidence, architecture/API/cache and migration notes, test and performance report, store assets/listing, release checklist, support runbook, known-issues register, and maintenance schedule.

## 9. Scope decisions to settle at kickoff

Confirm budget/timeline, Home placement, supported devices/OS versions, required media viewers, native secondary-content/form scope and data sources, backend/domain access, publishing identity and ownership/access arrangements, launch pricing/regions, and maintenance term/fees/coverage.

Working scope is the public Gignos experience plus the sample's saved-record feature. Android, new editorial/admin tools, new account systems, cross-device sync, payments, and full offline media downloads are not assumed. If discovery finds an existing public website flow requiring any of these, flag it in the parity checklist for an explicit decision rather than silently omitting it. An excluded workflow must not become a website handoff from the app.

Optional Spotlight, regional downloads, widgets, proximity features, AR placement, and interactive timelines follow the priorities document. They must not displace the required native parity work or reintroduce app-to-browser Handoff.
