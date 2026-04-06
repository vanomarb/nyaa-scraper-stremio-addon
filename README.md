# 🎌 Introduction of Nyaa Anime Scraper Stremio Addon

Hi everyone! I built an anime streaming addon for Stremio that connects to Nyaa.si and AnimeTosho, with Real-Debrid support.

I had a hard time finding a good addon for anime metadata—especially for donghua—so I ended up making my own. Sharing it here in case it helps anyone dealing with the same problem.


### What It Does
Searches Nyaa.si or AnimeTosho for anime torrents in real-time, automatically matches episodes, and streams them directly in Stremio. With Real-Debrid, instantly plays cached torrents at full speed. Without it, plays uncached torrents depending on seeders.

### Technical Highlights

**Smart Title Matching**
When searching for an anime, the addon dynamically generates multiple query variants by cross-referencing available metadata sources (such as original titles, English titles, romanizations, and other language variants). The number and quality of these variants depend on the metadata sources provided—adding APIs like TVDB or TMDB significantly improves accuracy and coverage.

Instead of trying all variants sequentially, it uses an **early-stop pattern**: it tests queries one by one and stops as soon as it finds results, then caches that entire group for future requests. This means subsequent searches using any of those titles will instantly hit the cache without re-scraping.

**Episode Filtering**
Automatically detects episode numbers from torrent titles and matches them to what you're watching.

**Real-Debrid Integration**
- Shows `[RD+]` for cached torrents → instant playback
- Shows `[RD]` for uncached torrent → queues on RD, displays "DOWNLOADING" status

### Getting Started

1. **Configure**: Visit `/configure` page
2. **Add Real-Debrid** (optional): Premium account required
3. **Add TVDB/TMDB keys** (optional): Improves metadata accuracy
4. **Install in Stremio**: Instant anime streaming!


### Features
✅ Nyaa.si + AnimeTosho support  
✅ Dynamic title matching (cross-referenced metadata, adaptive variants)  
✅ Improved accuracy with TVDB/TMDB integration  
✅ Real-Debrid instant playback  
✅ Movie, Season and Series filtering (auto-detect)  
✅ Token encryption (secure API keys)  
✅ Configurable sources/categories  

### Why This Approach?
- **Zero user data stored**: Fully stateless
- **Respectful scraping**: Rate-limited to avoid blocking
- **Smart caching**: Early-stop reduces API calls by ~93% for multi-title searches

Enjoy streaming! 🎬✨

## 🤝 Contributing & Community Feedback

Hey! Thanks for checking out the addon 🙌

Right now, this repo isn’t open for direct code contributions.  
Instead, this is a space for the community to **share ideas, report bugs, and help improve the addon**.

---

### 💡 Suggestions / Feature Requests

Got an idea? I’d love to hear it.

- Open an **Issue**
- Start the title with: `[Feature]`
- Try to include:
  - What you want to see
  - Why it would be useful
  - (Optional) examples or references

---

### 🐞 Bug Reports

Something not working right? Let me know.

- Open an **Issue**
- Use: `[Bug]`
- Include:
  - Steps to reproduce
  - What you expected to happen
  - What actually happened
  - Screenshots/logs if possible

---

### ❓ Questions / Discussions

Not sure about something? Want to ask or suggest casually?

- Open an **Issue**
- Use: `[Question]` or `[Discussion]`

---

### 📌 Notes

- This addon is currently **maintained only by the developer**
- Pull Requests probably won’t be reviewed or accepted (for now)
- Changes and improvements are based on community feedback

---

### ❤️ Final Note

Even if you’re not contributing code, your feedback helps a lot.  
Every suggestion, bug report, or discussion helps make this better for everyone.

Thanks for being part of it 🙏
