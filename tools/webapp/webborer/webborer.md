---
name: "webborer"
categories:
  - blackarch-webapp
version: "173.b323cf4"
homepage: "https://github.com/Matir/webborer"
packaged-by: [blackarch]
blackarch-builddate: "2021-06-24"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "Matir/webborer"
github-stars: 44
github-pushed: "2023-03-07"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webborer

> Portable Go directory-enumeration tool (the original "GoBuster") with HTML parsing, extension mangling, and SOCKS proxy support.

## What it's for

WebBorer discovers existing directories and paths on a web server by requesting wordlist entries and watching for hits. It was briefly named GoBuster before being renamed to avoid colliding with OJ Reeves' better-known tool. Distinguishing features: a compiled binary with no runtime, parsing returned HTML for further directories to spider into, exclusion of whole subpaths, wordlist mangling with extensions and case variants, HTTP authentication, and SOCKS 4/4a/5 proxy support. It does enumeration only — no vulnerability checks. Its README documents no examples; the flags below are verified against the packaged source (`settings/settings.go`).

## Typical use cases

### Enumerate a site with the built-in wordlist

Quick default directory sweep.

```
webborer -url https://example.com/
```

Outcome: requests each built-in wordlist entry under the URL with concurrent workers, reports discovered paths as they are found, and follows links found in returned HTML (`-html` is on by default).

### Use a custom wordlist with extension mangling

Target a specific technology's file extensions.

```
webborer -url https://example.com/ -wordlist dirs.txt -extensions .php,.bak
```

Outcome: probes each wordlist entry and additionally each entry with every listed extension appended (`-mangle` is on by default); `-cases` also tries case variants of the entries.

### Exclude subpaths

Skip known-noisy areas of the site.

```
webborer -url https://example.com/ -exclude /blog,/media
```

Outcome: paths under the excluded subtrees are never requested nor followed, keeping the crawl inside the interesting parts of the target.

### Scan several roots from a file

Enumerate across multiple bases in one run.

```
webborer -url_file targets.txt -wordlist dirs.txt
```

Outcome: each URL in the file is treated as a starting URL and scope; `-outfile results.csv` writes findings to a file instead of stdout, and `-proxy` routes all probing through a SOCKS proxy.

*No corpus evidence — derived from upstream source (Matir/webborer README and settings/settings.go flag definitions).*
