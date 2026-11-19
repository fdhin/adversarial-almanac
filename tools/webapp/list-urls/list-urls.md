---
name: "list-urls"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://www.whoppix.net/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: Python
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# list-urls

> Prints every link found on a web page, one URL per line.

## What it's for

list-urls fetches a URL, parses the HTML with sgmllib, and prints every `href` attribute it finds. It is a minimal single-file Python 2 script derived from the URL-extractor example in Mark Pilgrim's "Dive Into Python", packaged for quick one-shot link extraction. Reach for it when a page's raw link list is all you need and piping through a full crawler is overkill. It does not crawl (no recursion), does not deduplicate, only reads `<a href>` tags, and cannot see JavaScript-rendered links. There is no upstream repository — the package ships the script directly.

## Typical use cases

### Dump all links from a single page

```
list-urls http://example.com/
```

Outcome: prints each `href` found on the page, one per line; if the fetch fails it prints `Could not reach <url>` and reminds you to include the `http://` prefix.

No corpus evidence — derived from the packaged source (BlackArch `list-urls.py`, based on the Dive Into Python example by Mark Pilgrim).
