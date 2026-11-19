---
name: "nox-framework"
categories:
  - blackarch-scanner
version: "1.0.5"
homepage: "https://github.com/nox-project/nox-framework"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-31"
build-system: python
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "nox-project/nox-framework"
github-stars: 339
github-pushed: "2026-05-06"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 18
gh-activity-checked: "2026-11-09"
---

# nox-framework

> Async OSINT/CTI engine that pivots from one email, domain, username, hash or phone number across 120+ breach, dork and paste sources and writes scored identity reports.

## What it's for

NOX is a Python threat-intelligence engine for red-team recon and exposure analysis: give it one identifier and it queries its JSON-plugin intelligence sources in parallel, then (optionally) recursively re-injects everything it finds — usernames, emails, cracked passwords, phones — as new scan seeds, clusters the results into identity graphs, and exports PDF/HTML/JSON/CSV/Markdown reports. An OPSEC "Guardian" layer routes traffic through proxies or Tor, rotating keys per source. It is a passive collection tool: it queries public and breach-correlated sources about a target, it does not touch the target's own systems. API keys live in `~/.config/nox-cli/apikeys.json` (sources without keys are skipped silently); `--list-sources` shows what is configured. The console entry point is `nox-cli` (or `python3 nox.py` from a source checkout).

## Typical use cases

### Breach lookup for one identifier

```
nox-cli -t user@example.com
```

Outcome: auto-detects the input type (email/domain/IP/username/hash/phone) and queries the breach sources in parallel, printing correlated credentials and identity records; a plain `--no-pivot` variant stays at this phase only.

### Full recursive autoscan

```
nox-cli -t user@example.com --autoscan --depth 3
```

Outcome: runs the whole pipeline — breach scan, hash cracking, Google/Bing/SearXNG dorking, paste/Telegram scraping — re-injecting every discovered identifier up to the pivot depth cap, with global deduplication.

### Route collection through Tor or a proxy

```
nox-cli -t user@example.com --autoscan --tor
nox-cli -t user@example.com --autoscan --proxy socks5://127.0.0.1:1080
```

Outcome: all source traffic egresses through Tor (port 9050) or the given HTTP/S/SOCKS5 proxy instead of directly; `--guardian-off` disables the automatic proxy-rotation layer entirely.

### Single-purpose dorking or scraping

```
nox-cli --dork user@example.com
nox-cli --scrape user@example.com
```

Outcome: runs only the document-dorking phase (leaked files, `.env`s, dumps) or only paste-site/Telegram scraping, without the full pipeline.

### Identify and crack a hash

```
nox-cli --crack 5f4dcc3b5aa765d61d8327deb882cf99
nox-cli --crack 5f4dcc3b5aa765d61d8327deb882cf99 --no-online-crack
```

Outcome: fingerprints the hash type and cracks it via rainbow-table APIs plus local wordlists; `--no-online-crack` keeps the hash off third-party APIs. `--analyze 'Password1'` does standalone password-strength analysis.

### Produce a report

```
nox-cli -t user@example.com --autoscan -o report.pdf --format pdf
```

Outcome: writes an executive-style report (formats: `json`, `csv`, `html`, `md`, `pdf`) with a summary dashboard, identity clusters and pivot chains; JSON exports carry a self-describing `_meta` block.

### Audit sources and keys

```
nox-cli --list-sources
```

Outcome: prints the plugin table — source name, input type, confidence score, and whether its API key is configured — so you know which feeds will actually run.

### Work interactively

```
nox-cli
```

Outcome: drops into the REPL (`scan`, `dork`, `crack`, `graph`, `pivot <n>`, `export`, ...) for exploratory sessions where you pivot on individual results by number.

*No corpus evidence — derived from upstream documentation (README CLI reference of nox-project/nox-framework; `nox-cli` console script verified in setup.py).*
