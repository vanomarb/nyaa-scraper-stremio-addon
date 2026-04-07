# Changelog

All notable changes to the Nyaa Stremio Addon are documented here.

---

## [1.3.0] - Current Version

### Added
- **Outdated Addon Override** — When a user's installed addon config is from version ≤ 1.2.1 (or has no version tag), the stream endpoint returns a single informational stream instead of results: `⚠️ Outdated addon detected — Please reinstall the addon to refresh manifest or meta`
- **Config Version Embedding** — New installs from the configure page always embed `v=1.3.0` in the manifest URL config segment, enabling server-side version detection going forward.

### Fixed
- **Mirror Health — Inaccurate Failure Marking** — Mirror health tracking now distinguishes error types: HTTP 502/503/504 (upstream overloaded) no longer marks the mirror as unhealthy — the request is silently skipped. HTTP 429 backs off the mirror with a rate-limited cooldown. Only genuine connection or parse failures mark a mirror unhealthy.
- **Mirror Rotation — Infinite Loop** — Replaced the snapshot-length-gated mirror loop with a partition strategy: mirrors are split into `ready` (healthy or cooling-off expired) and still-cooling sets, then tried in that order. All mirrors are always considered, eliminating the infinite loop when only cooling-off mirrors remain.
- **Batch Torrent — Unwanted RD Downloads** — `batchResolver` no longer adds magnets or calls `selectFiles` at stream-list-build time. File introspection is now read-only (looks up existing RD library entries only). Magnet adding and file selection happen exclusively inside the resolve route when the user actually clicks a stream.
- **Batch Torrent — Episode File Index** — `_selectTorrentFiles` and `_createTorrentId` now receive and honour the specific `fileIndex`, so RD only downloads the requested episode file instead of every file in the pack.
- **Batch Torrent — Cached File Selection Overwrites** — `_resolveCachedFileIds` now always returns the single `fileIndex + 1` for integer indexes, preventing previously cached multi-file selections from re-triggering full-pack downloads.
- **Resolve Route — Duplicate RD Requests** — Added in-flight deduplication for the `/resolve` route. Concurrent requests for the same `infoHash:fileIndex` share one promise instead of racing to add the same magnet multiple times.
- **Resolve Route — Stremio HEAD Preflight** — Added an explicit `HEAD /resolve/:rdKey/:infoHash/:fileIndex/:title` handler that returns 200 immediately, preventing Stremio's URL-validation preflight from triggering unnecessary RD pipeline work.
- **Error Wrapping** — Several `Promise.reject(string)` calls converted to `Promise.reject(new Error(...))` for proper stack traces in error logs.

### Improved
- **Episode Number Parsing** — `parseFileEpisodeNumber` in `batchResolver` overhauled with a ported Amatsu-style parser: pre-sanitizes resolution/codec/audio/group noise, handles CJK episode markers (第01話, 01화), season-prefix guarding (S2 files excluded from S1 requests), and a right-to-left token scan as final fallback.
- **Fallback Stream Label** — When RD file list is unavailable (torrent not yet in library), the fallback stream descriptor now derives a clean display name from the pack title (strips group tags, resolution labels, year) plus the target episode number instead of showing `null`.

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
