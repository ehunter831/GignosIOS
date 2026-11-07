# Gignos iOS — feature priorities

Prepared October 18, 2026 • Revised November 7, 2026 • Companion to [Gignos-requirements-for-Eli.md](Gignos-requirements-for-Eli.md)

## Purpose

Eli's first priority is a complete native experience. Users must be able to finish the agreed Gignos browsing and research journeys inside the app. **The app must never hand them off to the Gignos website**, including for secondary pages, unsupported media, errors, or missing features.

The requirements document defines acceptance; this document sets build order. This revision removes the former website-fallback allowances. Optional native features follow parity.

**Working repository:** `ehunter831/GignosIOS`. Baseline: [main at 89649d4](https://github.com/ehunter831/GignosIOS/tree/89649d47964c8febf6f19f69a8677442c99617be), reviewed November 7, 2026. Findings below are from source inspection, not a fresh device test.

## Eli's itemized focus areas

| Priority | Area | Current gap | Required result |
| --- | --- | --- | --- |
| P0.0 | Remove website handoffs | “View on Gignos,” “Full entry on Gignos,” website menus, About links, and generic description-link handling | Every Gignos action resolves to native navigation; errors recover in-app |
| P0.1 | Images | System-symbol rows; no catalog photos or media model | Thumbnails, hero images, galleries, captions/credits, image caching |
| P0.2 | Native Home | Sites opens directly on the catalog | Introduction, search, map, recent sites, learning and research/contact entries |
| P0.3 | Search/filter/sort parity | Only type/country filters and name/country sorts | Function, classification, period, age filters/sort, taxonomy filters, correct complete counts |
| P0.4 | Media | No video/audio/model viewers in the reviewed details | Required media works inside the app; no website fallback |
| P0.5 | Universal Links | Entitlement exists, but no incoming router; Debug/Release bundle IDs differ | Outside links open the correct native screen; production association and device tests pass |
| P0.6 | Classification hierarchy | Flat list | Parent/child browsing with native records and related sites |
| P0.7 | Rich details and citations | HTML becomes plain text; extracted links use generic URL opening | Structured content, contextual references, complete native related-record journeys |
| P0.8 | Secondary content | About, News, Contact, Q&A, Privacy point to web pages | Native screens and any agreed public forms |
| P0.9 | Journey and release verification | Map, saved records, and cache exist; full acceptance is unverified | Consistent navigation, offline text, migrations, accessibility, iPad and physical-device evidence |

Preserve the existing SQLite/GRDB cache, full-text search, native catalogs/details, related-record navigation, map, sharing, and saved records. Extend this foundation rather than treating it as missing.

## Priority 0: version 1 parity

Start P0.0 and the P0.5 account/domain coordination immediately. Build images, Home, and filters next. Complete rich details, media, secondary content, and hierarchy before release. Run P0.9 throughout. Each item has specific acceptance criteria in the linked requirements section.

### P0.0 Remove every Gignos website handoff

Replace the catalog/detail website actions, website menu, and About website links. Route internal/relative links and citations through a shared native resolver. This rule also applies to Home cards, saved records, media failures, unknown routes, and offline/error states.

A Safari sheet or full-page web view of Gignos does not count as native parity. Share/copy may still produce canonical HTTPS URLs without navigating away. API/media requests are normal data loading. A contained media or third-party reference viewer must not replace a Gignos screen or trigger automatic external navigation.

**Done when:** every app journey and error path stays inside the native experience; regression coverage includes the existing exit points. See [§2.1](Gignos-requirements-for-Eli.md#21-keep-every-gignos-journey-native--first-priority).

### P0.1 Photos and galleries

Extend the record model, decoder, and persistence for hero/gallery media. Add thumbnails, hero images, swipe/zoom galleries, captions/credits, lazy loading, sizing, caching, and useful missing-image states. A failed image never blocks reading.

**Done when:** records with multiple images, missing images, failed requests, and cached revisits all work. See [§2.3](Gignos-requirements-for-Eli.md#23-photos-hero-images-and-galleries).

### P0.2 Native Home

Add the introduction, search and map entries, recently added photo cards, and learning/research entries. Learning leads to native exploration; research leads to native information/contact. Confirm Home placement inside Sites and the backend's “recently added” definition. Do not substitute `updatedAt` for creation date without confirmation.

**Done when:** a new user can discover content and complete these paths without a website exit. See [§2.2](Gignos-requirements-for-Eli.md#22-native-home-and-discovery).

### P0.3 Full search, filter, and sort parity

Add site function, classification, time period, age-range controls and age sorting, plus applicable taxonomic filters. Audit all five catalogs. Extend typed data, relationships, SQLite queries/indexes, and migrations; the current cache does not make this solely UI work.

**Done when:** tested queries match intended website semantics and complete record sets, including age boundaries, unknown dates, combined filters, resets, counts, and map/list consistency. See [§2.4](Gignos-requirements-for-Eli.md#24-complete-catalogs-search-filters-and-sorting).

### P0.4 Video, audio, and other media

Inventory actual formats/providers and rights. Implement native players or contained item-level embeds, with accessible controls and a clear native return path. Required basic model viewing belongs here; optional AR placement remains P2.2.

**Done when:** each required type works in-app and failure/offline states preserve the record. An unavailable message cannot stand in for an unimplemented required viewer. See [§2.6](Gignos-requirements-for-Eli.md#26-video-audio-and-embedded-media).

### P0.5 Universal Links — start coordination now

Universal Links let a normal Gignos HTTPS link, opened from outside the app, take the user directly to its native content when iOS/user choice permits. People without the app can use the same shared URL on the website. Internal app navigation must resolve directly to native screens; it must never open a browser hoping to return to the app.

Eli owns the signed app identity, entitlement, URL router, and tests. The website owner deploys the matching AASA association file. An entitlement alone does not complete the feature. Resolve the current Debug `com.gignos.ios` / Release `com.ggignos.ios` difference before configuring production association.

**Done when:** the route matrix, production association, cold/warm launches, correct tabs/details, numeric source IDs, offline/error handling, and physical-device tests pass. See the full definition, ownership, route patterns, and test plan in [§2.9](Gignos-requirements-for-Eli.md#29-universal-links--bring-users-into-the-app), with [Apple's overview](https://developer.apple.com/documentation/xcode/allowing-apps-and-websites-to-link-to-your-content).

### P0.6 Classification hierarchy

Retain ancestry/parent relationships and rank in the model. Add hierarchy browsing, node details, related sites, and appropriate filters without inventing missing taxonomy.

**Done when:** users can navigate multiple levels and return to the same hierarchy position. See [§2.8](Gignos-requirements-for-Eli.md#28-classification-hierarchy).

### P0.7 Rich details, citations, and relationships

Preserve meaningful rich text and contextual reference markers. Route citations and internal description links natively. Verify the site → classification/period/culture → related site and site → source journeys across all five catalogs.

**Done when:** required descriptions, metadata, provenance, authors, and references are available and usable without “full entry” website links. See [§2.5](Gignos-requirements-for-Eli.md#25-rich-details-citations-and-related-record-journeys).

### P0.8 Native secondary pages and contact

Replace web destinations for About, News/articles, Contact, Q&A, and Privacy. Include native research information/contact. Agree content feeds and any public form integrations with the backend/content owners. Record excluded flows explicitly and omit their actions rather than sending users to the website.

**Done when:** every secondary destination and included form completes in-app. See [§2.7](Gignos-requirements-for-Eli.md#27-native-secondary-information-and-contact).

### P0.9 Complete-journey and release verification

Verify map/list consistency, preserved tab/search/scroll state, saved text across restarts/upgrades, cache migrations, partial-data labeling, and offline/failed-refresh recovery. Run accessibility/iPad/device checks and performance measurements alongside implementation. Maintain the parity checklist with acceptance evidence.

**Done when:** all required journeys pass and no app task hands off to Gignos web content. See [§2.10](Gignos-requirements-for-Eli.md#210-map-saved-records-offline-behavior-and-navigation) and requirements §§3–8.

## Priority 1: optional native additions after parity

These build on P0. Include them in version 1 only after required parity is complete and if the schedule allows; otherwise make them the first update.

### P1.1 Spotlight
Index cached records so a search for "Lascaux" in iOS Search opens the native record. Reuse the P0.5 router. App-to-browser Handoff is excluded; it must not reintroduce a website exit.

### P1.2 Offline trip download
Builds on the existing cache: "Download this region" stores the text plus hero images for every site in a country or region before a trip. Many sites are remote with poor coverage. Full offline video stays out of scope (§9).

### P1.3 Site of the Day widget
A home-screen widget with a site's photo, name, and age that opens the record. A daily reason to come back. Depends on P0.1.

## Priority 2: version 1.1 headline features

These are the features that make the app a better way to explore prehistory than the website. Each needs a short design review with Derek before building.

### P2.1 Near me and travel mode
"Sites near me" plus an optional alert when the user is traveling near a catalogued site.

Guardrails, which are not negotiable:
- Use only the same deliberately imprecise public coordinates as the website (see the [Sites location policy](https://gignos.com/sites)).
- No turn-by-turn directions to sites. Directions only to a public museum or visitor center if the data supports one.
- Location permission is "while using" by default. Any travel alerts are opt-in, and proximity is processed on the device.
- Update the App Store privacy disclosures (§6).

### P2.2 3D models in AR
The website promises 3D models. Show supported models with Apple's AR Quick Look (USDZ), so a life-size Laetoli footprint or a skull can be placed in the room. Depends on the P0.4 inventory: confirm source formats, conversion to USDZ, and **rights to redistribute each model in an app**.

### P2.3 Interactive deep-time timeline
A scrubbable timeline from about 3.6 million to 10,000 years BP. As you drag, the map and list show the sites and cultures active at that time. This is the signature screen most likely to make the app memorable, and the largest design effort on this list. It needs consistent date data, so date conventions remain as supplied (§2) until the backend owners decide otherwise.

## Priority 3: later

- **Personal notes and collections** on saved records (e.g. "Neanderthal sites in Iberia"), with Apple Pencil markup on iPad. Local only. Sync is out of scope (§9).
- **New-site notifications.** Needs a backend signal or push infrastructure. Revisit after launch.

## Scope exclusions do not create website exits

New accounts, editorial/admin tools, and cross-device sync remain outside assumed version 1 scope. Inventory public contribution/submission, newsletter, comparison, and export tasks during discovery; any exclusion needs an explicit decision in the parity checklist. These are not blanket approved exceptions.

About, News, Contact, Q&A, and Privacy are native requirements. A deferred task must not appear as a button that sends the user to the website. Canonical website pages remain available for external discovery and recipients without the app; that does not authorize an outgoing app handoff.

## Long-lead items for Derek

Start these now. They depend on people and accounts, not code.

1. **Apple Developer account and verified production app identity** (Eli). Resolve the Debug/Release bundle-ID discrepancy; this identity must match the AASA file and signed release.
2. **AASA file on gignos.com** (website owner, coordinated by Eli). See P0.5 and requirements §2.9.
3. **Backend repository access** for Eli (`gignos/gignos-api`). Confirms search/filter semantics, the "recently added" definition, media types, enum meanings, and native secondary-content/form data sources (§5).
4. **Media and 3D rights review** before images, video, and models ship in a distributed app.

## Housekeeping

- `README.md` and `docs/API.md` in Eli's repository still say there is no disk catalog cache. Update them to describe the SQLite cache.
- The working app uses GRDB. Record the dependency decision and keep dependency/cache documentation current.
- **Toolchain:** verify the checked-in GRDB version's toolchain requirements and document one supported Xcode/Swift setup for local development and Xcode Cloud.

## Decisions needed

1. Confirm Home as the opening page inside Sites (P0.2).
2. Approve P1 items for version 1 or the first update.
3. Confirm secondary-content feeds, included public forms, required media viewers, and owners for backend/domain dependencies.
4. Approve the P2 guardrails for location features before design starts.

The no-website-handoff rule is a requirement, not an open decision.
