# Changelog

All notable changes to the Nyaa Stremio Addon are documented here.

---

## [1.6.1] - Patch: TorBox & Source Tag Fixes

### Fixed
- **TorBox Availability Check** — Fixed TorBox cached torrent detection using the wrong request format. Availability checks now work correctly, properly showing which torrents TorBox can deliver instantly.
- **Provider Tag Missing on Cached Results** — Torrents loaded from cache were losing their source tag (e.g. "nyaa"), causing them to display incorrectly in stream results. This is now preserved correctly.

---

## [1.6.0] - Smarter Anime Search

### Added
- **Absolute Episode Matching** — When searching for a seasonal episode (e.g. Season 4 Episode 97), the addon now also searches by the absolute episode number (e.g. Episode 182) using cached series metadata. This finds fansub releases that use continuous episode numbering instead of season/episode notation.
- **Version Badge** — The configure page now shows the current addon version next to the Stremio badge. Click it to view what changed in this release.

### Improved
- **Search Result Caching** — Priority search patterns (like "Show S04" and "Show Season 4") are now cached for 5 minutes, reducing repeated requests to Nyaa for the same episode.
- **Nyaa Source Simplified** — Removed unreliable mirror fallback logic. Requests now go directly to nyaa.si for faster, more predictable results.

---

## [1.5.0] - Multi-Debrid Support

### Added
- **TorBox & AllDebrid Support** — You can now use TorBox or AllDebrid as your debrid service in addition to Real-Debrid. Switch between services using the new dropdown on the configure page — no more manual URL editing.
- **API Key Validation** — The Install button now validates all your API keys (debrid, TVDB, TMDB) before showing install options. If a key is wrong or expired, you'll see a clear error message pointing to exactly which key failed and why.

### Improved
- **Cleaner Configuration URLs** — All service tokens now use consistent named parameters (e.g. `rd=TOKEN`, `torbox=TOKEN`, `ad=TOKEN`) instead of a legacy positional format. URLs are shorter and easier to read and share.

---

## [1.4.2] - Performance Optimization

### Improved
- **Faster Stream Loading (Cache Miss)** — Optimized parallel processing of torrent fetches, metadata lookups, and Real-Debrid availability checks. Stream lists now appear in 8-12 seconds instead of 30-40+ seconds when cache isn't available.
- **Reduced Timeout Waits** — Stricter 10-second timeout on Real-Debrid operations prevents hanging indefinitely on slow connections. Falls back gracefully if RD is unreachable.

### Internal
- **Parallelized Batch Operations** — Multi-file torrent expansion now processes multiple torrents simultaneously instead of sequentially, dramatically reducing wait time.
- **Batched RD API Calls** — Real-Debrid info requests now batch (5 concurrent) instead of queuing individually, cutting availability checks from 20+ seconds to 3-5 seconds.
- **Background Metadata Fetching** — Series metadata fetches in parallel with stream building, improving responsiveness on first load.
- **Better Error Resilience** — One slow/failing torrent no longer blocks results from other torrents; degradation is graceful.

---

## [1.4.1] - Patch: Resolution Filter Persistence Fix

### Fixed
- **Resolution Filter Not Prefilled** — Resolution checkboxes now correctly restore their unchecked state when loading a saved configuration. Previously all checkboxes defaulted to checked regardless of saved preferences.

---

## [1.4.0] - P2P Streaming & Resolution Filters

### Added
- **P2P Direct Magnet Links** — Optional P2P streaming mode lets you watch without Real-Debrid. Direct peer-to-peer connections provide instant playback without needing to wait for RD caching. Also works with trackers for faster peer discovery. Toggle in Optional Settings (only shows when no RD key is configured).
- **Video Resolution Filter** — Choose which resolutions to include when searching (4K, 1440p, 1080p, 720p, 480p). Rejected resolutions are excluded from results entirely, so you only see streams that match your quality preferences. Uncheck unwanted resolutions in Optional Settings.
- **Responsive Optional Settings Modal** — Settings modal now uses 3-column layout on larger screens (while keeping 2 columns on mobile) for a cleaner, more spacious interface.

### Improved
- **Faster P2P Peer Discovery** — Magnet links are now enriched with comprehensive DHT trackers when P2P mode is active. Reduces initial peer connection time from 30+ seconds to 5-10 seconds, making P2P streams practical for immediate playback.
- **Configuration Management** — Added URL parameters `p2p=true` and `resolutions=4K,1080p,720p` for persisting user preferences in saved configurations.

### Fixed
- **P2P Mutual Exclusivity** — P2P toggle automatically hides when an RD key is present (they don't work together). If RD key is cleared, P2P option reappears. RD always takes priority if both settings somehow exist in a URL.

### Internal
- **Tracker Enrichment** — Integrated existing `getMagnetLink()` from httpHelper to provide optimal tracker set (fetches from GitHub + anime-specific trackers) instead of a static list.
- **Resolution Filtering** — Applied at stream-building stage, filtering after episode/season matching but before stream output for efficiency.

---

## [1.3.2] - Internal Refactor

### Changed
- **Unified Logging System** — Implemented centralized logger with 3-level control (`LOG_LEVEL=1|2|3`), replacing scattered `console.*` calls with consistent `[DEBUG]`, `[INFO]`, `[WARN]`, `[ERROR]` prefixes
- **Code Quality Improvements** — Removed 10 dead code files and unused exports; reduced `src/` from 37 → 27 files through intelligent module merging
- **Reduced Comment Verbosity** — Trimmed verbose JSDoc and inline comments to short, actionable summaries per method/block
- **In-Memory Cache Abstraction** — Extracted `createMemCache()` factory for consistent cache behavior across all metadata sources (Kitsu, IMDB, TMDB, TVDB)

### Fixed
- **TVDB ID Resolution Bug** — Fixed `tvdbIdFromImdb()` using hardcoded URL; now correctly uses dynamic `imdbId` parameter

### Internal
- **Module Consolidation** — Merged 5 tiny utilities into `helpers.js`, 2 limiters into `limiters.js`, and 2 DB files into single `db/index.js`
- **Module Refactoring** — Absorbed `cache/metadataService.js` into `cache/metadata.js` for improved cohesion
- ✅ All verifications passing: app startup clean, logging levels functional, `console.*` completely removed, full test coverage

---

## [1.3.1] - Patch Release

### Added
- **Smart Torrent Title Recognition** — The addon now uses intelligent matching to extract season, episode, and title information from torrent filenames. It recognizes Japanese, Chinese, and Korean anime titles with high accuracy, handles various naming conventions (different formatting, symbols, and abbreviations), and automatically corrects common parsing mistakes. This means it finds the right episodes even when torrent names are messy or use unusual formatting.
- **Accurate File Sizes for Batch Downloads** — When you select an episode from a multi-file pack, the addon now shows the estimated file size for just that episode instead of the whole pack size. Makes it easier to know what you're getting before downloading.
- **Improved Stream Information** — Stream listings now show clearer episode numbers, titles, file sizes, and quality information. Better icons and formatting make it easier to spot what you want at a glance.
- **Faster Streaming** — Direct playback links are now cached, so if you replay the same episode, it loads instantly without contacting Real-Debrid again.

### Fixed
- **Better Error Messages** — Real-Debrid errors are now properly reported, making it easier to troubleshoot issues if something goes wrong.
- **Metadata Without Real-Debrid** — Episode lists and metadata now work correctly even if you haven't connected a Real-Debrid account.
- **Stable Nyaa Access** — Nyaa mirror selection is more reliable and handles temporary outages better.

---

## [1.3.0] - Patch Release

### Fixed
- **Real-Debrid Auto-Downloads** — Pack torrents (multiple episodes) no longer automatically download all files when browsing for streams. Only the episode you select will be downloaded, saving bandwidth and avoiding cluttered RD libraries.
- **Single Episode Selection** — When you click a specific episode from a multi-file torrent pack, Real-Debrid now only downloads that one episode instead of all files in the pack.
- **Faster RD Queries** — Eliminated duplicate requests to Real-Debrid that were slowing down stream playback. Each file is checked once, delivering results faster.
- **Nyaa Mirror Reliability** — Fixed mirror selection to handle temporary service issues better. Mirrors that are temporarily overloaded are now retried instead of being marked permanently broken.
- **Episode Number Detection** — Better at parsing episode numbers from filenames, including Asian formats (Japanese, Chinese, Korean characters). Works with more torrent naming styles.
- **Cleaner Stream Names** — Stream display names are now clean and readable, showing episode info instead of raw technical details.

---

## [1.2.1]

### Fixed
- **Batch Torrent Handling** — Multi-file torrents now return only individual episode streams, removing the redundant pack-level stream.
- **Stream Labels** — Improved stream labels to include the filename of the selected episode for better clarity.

---

## [1.2.0] - Feature Update

### Added
- **Language Filtering & Detection** — Automatic language detection using `franc` library to identify content language (Japanese, English, etc.)
- **Cross-Reference Metadata** — Unified resolver supporting Kitsu, TVDB, TMDB, and IMDB with automatic cross-linking when primary source fails
- **Anime-Only Content Filtering** — Genre-based anime detection across all metadata sources:
  - Kitsu: Always anime-only database
  - TVDB: Checks for "Anime" genre tag
  - TMDB: Checks for "Anime" or "Animation" genre tags
  - IMDB/Cinemeta: Checks for "Animation" genre tag
- **Enhanced Movie Query Generation** — Improved search patterns for movie titles including alternate title combinations with separators (dash, colon)
- **TMDB Fallback for IMDB** — When Cinemeta fails to resolve an IMDB ID, automatically attempts TMDB cross-reference lookup for richer metadata
- **Genre Extraction** — All metadata sources now extract and normalize genre information for filtering and detection

### Improved
- Movie search queries now focus on individual title variants plus meaningful combinations, avoiding redundant permutations
- Metadata resolution now prioritizes anime-specific sources (Kitsu) and validates content type before processing
- Request pipeline includes early-exit for non-anime content at both stream and meta endpoints

---

## [1.1.0]

### Added
- **MongoDB Caching Layer** — Persistent request cache using MongoDB Atlas with TTL-based auto-cleanup:
  - Search cache (4-hour expiration) for torrent queries
  - Availability cache (5-day expiration) for Real-Debrid status
  - Automatic migration system for schema changes
- **In-Flight Request Deduplication** — Concurrent identical requests reuse same promise instead of duplicating API calls
- **Early-Stop Query Pattern** — Sequential title variant execution stops immediately on first successful result (≥1 match), reducing API calls by ~93%
- **Rate Limiting with Bottleneck** — Per-site request throttling:
  - Nyaa: Serial 1-per-second requests
  - AnimeTosho: 2 concurrent with 500ms gap
- **Worker Queue System** — Global stream handler queue and per-request query queue for controlled parallelism

### Improved
- Replaced redundant per-query individual caching with unified group cache keyed by MD5 hash of sorted queries
- Removed duplicate database entries: from 14+ documents per show to 1 group cache document
- Enhanced logging with prefixed categories (`[Stream]`, `[Cache]`, `[Nyaa]`, etc.) for better diagnostics

### Changed
- Removed `getOrFetchSearch()` wrapper function — simplified to direct rate limiter calls
- Merged all caching logic into `getOrFetchSearchGroup()` for single source of truth

---

## [1.0.0]

### Added
- **Core Torrent Streaming** — Nyaa.si and AnimeTosho torrent source integration with Stremio addon framework
- **Real-Debrid Integration** — Instant availability checking and torrent-to-direct-link resolution
- **Multi-Source Metadata** — Kitsu primary anime database with fallback to TVDB and TMDB
- **Episode Caching** — Server-side episode list caching with Real-Debrid availability indicators (⚡)
- **Configurable API Keys** — User-supplied TVDB and TMDB API keys via addon configuration page
- **Title Matching & Normalization**:
  - Roman numeral ↔ Arabic number conversions (e.g., "Season II" ↔ "Season 2")
  - Title part splitting on colon/dash separators
  - Bracket/parentheses tag stripping
  - Fuzzy title similarity matching for robust torrent filtering
- **Stremio Catalog** — Browse anime titles from Kitsu with poster artwork and metadata
- **Series Meta Object** — Full series information with genre, year, status, rating per Stremio spec

### Notes
- Initial stable release with foundational streaming pipeline
- Supports anime series and movies via kitsu, tvdb, tmdb and IMDB routing protocols

---

## Version Format

Versions follow semantic versioning: `MAJOR.MINOR.PATCH`
- **MAJOR**: Breaking changes or new content sources
- **MINOR**: New features or significant improvements
- **PATCH**: Bug fixes and minor enhancements
