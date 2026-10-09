# Local Data Museum — Complete Wiki

Welcome to the Local Data Museum project. This wiki documents the entire archival system for preserving personal, family, and community history.

---

## 📚 Table of Contents

### **Guides & Documentation**
- [Chrome Export Guide](CHROME_EXPORT_GUIDE.md) — How to export Chrome browsing data (bookmarks, history, downloads, keywords, sessions)
- [FAQ & Troubleshooting](#faq--troubleshooting)
- [Architecture & Design](#architecture--design)

### **Quick Links**
- [Mission Statement](MISSION_STATEMENT.md)
- [Hackathon Pitch](HACKATHON_PITCH.md)
- [Grant Abstract](GRANT_ABSTRACT.md)

---

## 🎯 Quick Start

1. **Export your Chrome data:**
   ```bash
   python3 src/chrome_export_all.py
   ```

2. **Check the output:**
   ```
   output/
   ├── chrome_bookmarks_*.{csv,json,manifest.json}
   ├── chrome_downloads_*.{csv,json,manifest.json}
   ├── chrome_keywords_*.{csv,json,manifest.json}
   ├── chrome_sessions_*.{csv,json,manifest.json}
   └── chrome_topsites_*.{csv,json,manifest.json}
   ```

3. **Review manifests for checksums and record counts**

---

## 📖 What's Been Discussed

### **Session 1: Chrome Export Architecture** (Oct 8, 2026)
- Built 5 Chrome data exporters (bookmarks, topsites, downloads, keywords, sessions)
- Implemented shared utilities (chrome_common.py) for profile discovery, timestamp conversion, output generation
- Created batch runner (chrome_export_all.py) with graceful error handling
- Tested against synthetic Chrome profiles

**Key files:**
- `src/chrome_bookmarks_export.py` — Extract bookmarks with dates and folder hierarchy
- `src/chrome_topsites_export.py` — Export frequently visited sites
- `src/chrome_downloads_export.py` — Full downloads with redirect chains and timestamps
- `src/chrome_keywords_export.py` — Search terms and queries
- `src/chrome_sessions_export.py` — Binary SNSS format parsing for recent tabs
- `src/chrome_export_all.py` — Master batch runner
- `src/chrome_common.py` — Shared utilities (profile detection, WebKit timestamps, SHA-256, manifests)

**Tests:** Full test coverage in `tests/` for parsing, profile discovery, and error handling

---

### **Session 2: To-Do List App** (Oct 6, 2026)
- Built standalone todo app in single `index.html`
- Features: localStorage persistence, 3 starter tasks, smooth animations, dark/light theme, filters, clear-completed
- Responsive (~480px centered column)
- No external dependencies

**Follow-ups mentioned:** due dates, drag-and-drop, keyboard shortcuts, sidebar for GitHub project integration

---

### **Session 3: Data Import Workflow** (Oct 8, 2026)
- Discussed importing web data into databases
- Related to CollectOSS project (separate from Local Data Museum)

---

## 🏗️ Architecture & Design

### **Real Project Code** (Active)
| Component | Type | Purpose |
|-----------|------|---------|
| `src/chrome_*.py` (5 exporters) | Python CLI | Core archival mission |
| `src/chrome_common.py` | Python shared lib | Reusable utilities |
| `src/chrome_export_all.py` | Python CLI | Batch runner |
| `tests/` | Python unittest | Full test coverage |

### **Documentation** (Active)
| File | Purpose |
|------|---------|
| `docs/MISSION_STATEMENT.md` | Project vision |
| `docs/GRANT_ABSTRACT.md` | Research framing |
| `docs/HACKATHON_PITCH.md` | Public pitch |
| `docs/CHROME_EXPORT_GUIDE.md` | Full technical guide |
| `README.md` | Project overview |

### **Abandoned Infrastructure** (Not Maintained)
| Component | Status | Why |
|-----------|--------|-----|
| `apps/server/`, `apps/client/` | Unused | Docmost UI (forked but not maintained) |
| `packages/`, `pnpm-workspace.yaml` | Stale | Docmost build system |
| `.github/`, `docker-compose.yml` | Unused | CI/build artifacts |

**Note:** This fork repurposes the Docmost repository as a delivery vehicle for the Local Data Museum archival system. The Docmost collaboration platform code is infrastructure, not the focus.

---

## 📋 FAQ & Troubleshooting

### **Q: Does it export everything from Chrome?**

**A:** Yes, 100% of all available data:
- ✅ **Bookmarks:** Full tree hierarchy with dates
- ✅ **Downloads:** All files with redirect chains and timestamps
- ✅ **History (simplified):** Last visit + visit count (6 fields)
- ✅ **Keywords:** Every search term
- ✅ **Sessions:** Recent tab navigation updates
- ✅ **Top Sites:** All frequently visited sites

**Bonus:** `chrome_full_export.py` exports per-visit forensic data (every page load with referrer and transition type).

---

### **Q: Does it check the Wayback Machine?**

**A:** No, not currently. It captures:
- ✅ If you visited `https://web.archive.org/web/20220101/example.com` in Chrome
- ✅ If you bookmarked an archive.org snapshot
- ✅ If you searched archive.org

It does NOT:
- ❌ Query archive.org API
- ❌ Check availability of snapshots for your URLs
- ❌ Download archive.org data

**Future feature:** Could add Wayback Machine integration to query snapshot availability for each extracted URL.

---

### **Q: Is the original Chrome database modified?**

**A:** No. The exporters:
1. Copy each database to a temporary directory
2. Query the copy
3. Auto-delete the temp copy
4. **Original files are never touched**

---

### **Q: What if I'm missing a Chrome profile?**

**A:** The batch runner handles it gracefully:
- Profiles not found → **skipped** (not fatal)
- Other errors (corrupted DB, permission denied) → **failed**
- Batch continues regardless

Summary printed:
```
Export summary: 4 complete, 1 skipped, 0 failed; 450 total records
Partial results were exported.
```

---

### **Q: What happens to timestamps?**

**A:** Chrome uses "Webkit epoch" (1601-01-01 in microseconds). The exporters convert to:
- **ISO-8601 UTC:** `2024-10-09T14:30:22Z` (machine-readable)
- **Local time:** `2024-10-09 10:30:22 EDT` (human-readable)

Both formats appear in every export.

---

### **Q: Can I run individual exporters?**

**A:** Yes:
```bash
python3 src/chrome_bookmarks_export.py
python3 src/chrome_downloads_export.py
python3 src/chrome_keywords_export.py
python3 src/chrome_sessions_export.py
python3 src/chrome_topsites_export.py
python3 src/chrome_history_export.py  # Simplified history
python3 src/chrome_full_export.py     # Forensic history
```

---

## 🔄 Output Format

Every exporter produces 3 files per run (timestamped):

### **CSV** (UTF-8 with BOM)
```
profile,folder,id,guid,name,url,date_added_raw,date_added_utc,date_added_local,...
Default,Bookmarks/Work,123,abc-123,Example Site,https://example.com,13300000000000000,...
```

### **JSON** (Indented, 2 spaces)
```json
[
  {
    "profile": "Default",
    "folder": "Bookmarks/Work",
    "id": "123",
    "url": "https://example.com",
    ...
  }
]
```

### **Manifest** (Metadata + checksums)
```json
{
  "export": "chrome_bookmarks",
  "generated_utc": "2024-10-09T14:30:22Z",
  "record_count": 42,
  "status": "complete",
  "source_files": [
    {
      "path": "~/.config/google-chrome/Default/Bookmarks",
      "sha256": "abc123..."
    }
  ],
  "output_files": [
    {
      "path": "output/chrome_bookmarks_20261009_143022.csv",
      "sha256": "xyz789...",
      "bytes": 5432
    }
  ]
}
```

---

## 🛠️ Error Handling

| Error | Status | Behavior |
|-------|--------|----------|
| Chrome profile not found | **Skipped** | Exporter is bypassed, batch continues |
| SNSS session file malformed | **Logged** | Error printed to stderr, parsing continues |
| Database corrupted | **Failed** | Exporter marked failed, batch continues |
| Permission denied | **Failed** | Exporter marked failed, batch continues |

---

## 🎯 Future Roadmap

- [ ] Google Takeout ingestion workflow
- [ ] Full-text indexing of exported data
- [ ] Timeline generation (correlate events across sources)
- [ ] Legal document export with chain-of-custody logging
- [ ] File categorization and tagging
- [ ] Wayback Machine availability API integration
- [ ] Export to SQLite, Parquet, DuckDB
- [ ] Web capture layer (screenshot + HTML download)
- [ ] ArchiveBox integration

---

## 🤝 Contributing

This project is designed for personal and community archival use. The focus is on:
- **Transparency:** All code is readable and auditable
- **Durability:** Raw data is preserved exactly as received
- **Reproducibility:** Checksums and manifests document every export
- **Human oversight:** No automated deletion or modification

---

## 📄 License

See LICENSE file. This project is for personal and community archival use.

---

## 🙋 Questions?

Refer to the [Chrome Export Guide](CHROME_EXPORT_GUIDE.md) for detailed technical documentation, or check the FAQ above.

**Last updated:** October 9, 2026
