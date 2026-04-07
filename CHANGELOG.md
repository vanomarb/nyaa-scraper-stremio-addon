# Changelog

All notable changes to the Nyaa Stremio Addon are documented here.

---

## [1.2.1] - Current Version

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
