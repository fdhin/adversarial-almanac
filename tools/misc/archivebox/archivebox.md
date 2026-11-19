---
name: "archivebox"
categories:
  - blackarch-misc
version: "903.59da482"
homepage: "https://github.com/pirate/ArchiveBox"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "pirate/ArchiveBox"
github-stars: 28973
github-pushed: "2026-11-07"
github-archived: false
github-issues: 160
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4679
gh-activity-checked: "2026-11-09"
---

# archivebox

> Self-hosted web archive: save pages as HTML, PDF, screenshots, media and more.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ArchiveBox is an open-source, self-hosted archiving system: you feed it URLs (one at a time, from files, stdin, browser exports, RSS) and it stores each page as ordinary files in a local collection — a SingleFile HTML render, a headless-Chrome PDF and screenshot, a wget clone with WARC, extracted article text, media via yt-dlp, and even git clones of linked repositories. A built-in web UI (`archivebox server`) plus a CLI and Python API manage the collection. It preserves content, not trust: archived JavaScript stays dangerous to replay, and sites that actively block archiving may need per-site workarounds. Use it to keep durable, offline copies of pages you cite or expect to disappear.

## Typical use cases

### Start a collection and archive a page

Initialize once per collection directory, then add URLs.

```
archivebox init
archivebox add 'https://example.com/some/page'
```

Outcome: `init` creates the data directory and index; `add` fetches the page and writes a snapshot folder containing the single-file HTML, PDF, screenshot and metadata. Repeat `add` for further pages.

### Bulk-import a list of URLs

Ingest URLs from stdin or any text file containing them.

```
cat urls.txt | archivebox add
```

Outcome: every URL found in the input (plain lists, HTML, RSS, JSON — the parser picks them out of arbitrary text) is queued and archived; already-snapshotted URLs are skipped by default.

### Crawl one level deep

Follow links off a seed page to capture what it references.

```
archivebox add --depth=1 'https://example.com'
```

Outcome: archives the seed page plus every linked page on the same output path set, multiplying coverage for entry pages like link lists or feeds; deeper crawls grow quickly, so scope with `--depth` deliberately.

### Serve the web UI for browsing the archive

Run the collection's viewer.

```
archivebox server 0.0.0.0:5797
```

Outcome: starts the Django server; the collection is browsable at `http://127.0.0.1:5797` with search, tags and snapshot replay. Guests see nothing public by default — permissions are widened via `archivebox config --set PUBLIC_INDEX=True` and friends.

### Export the collection index

Get the snapshot list out in a machine-usable format.

```
archivebox list --csv=timestamp,url,title > index.csv
```

Outcome: writes one row per snapshot with the requested columns; `--json` and `--html` variants emit the same index as a JSON blob or a static HTML table for keeping a published copy of what the archive contains.
