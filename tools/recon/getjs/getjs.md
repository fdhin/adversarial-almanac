---
name: "getjs"
categories:
  - recon
version: "git (2026-08-29)"
homepage: "https://github.com/003random/getJS"
origin: "github"
recipe: "upstream"
packaged-by: []
build-system: go
meta-checked: "2026-11-09"
github-repo: "003random/getJS"
github-stars: 902
github-pushed: "2026-08-29"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 1
---

# getjs

> Go CLI that extracts every JavaScript source URL from a page, a URL list or a piped HTTP response, optionally completing relative paths and resolving the files themselves.

## What it's for

getJS (003random, v2) gathers the JavaScript attack surface of web targets. Point it at a URL, a set of URL files, or pipe it any HTTP response, and it parses out script sources (and other extraction points) faster than grepping by hand — multithreaded (`-threads`, default 2) with control over request method, headers and timeout. `-complete` fills relative URLs out to absolute ones using the page origin, and `-resolve` then fetches each resolved source so you get the actual JS files rather than just references (the pair is how you build a corpus for secret-hunting tools). It is an extractor only: it collects and filters URLs, it does not analyze their contents.

## Typical use cases

### List JS sources of a single page

Quick answer for one target.

```
getJS -url https://example.com
```

Outcome: fetches the page, extracts every JavaScript source reference, and prints the URLs to stdout, one per line.

### Extract from an arbitrary HTTP response

Work on captured or crafted responses without refetching.

```
curl -s https://example.com | getJS
```

Outcome: reads the HTTP response from stdin and prints the JavaScript sources found in it — handy inside pipelines where the response is already in hand.

### Process URL lists in bulk

Sweep a bug-bounty scope file.

```
getJS -input urls.txt -threads 10
```

Outcome: processes every URL in `urls.txt` (repeat `-input` for more files) using 10 concurrent workers and prints the deduplicated source URLs for the whole list.

### Get absolute, resolved JS URLs (or files)

Turn references into a clean, fetchable list.

```
getJS -url https://example.com -complete -resolve -output js_urls.txt
```

Outcome: `-complete` rewrites relative sources to absolute URLs against the page origin, `-resolve` verifies/fetches each, and the results are written to `js_urls.txt` instead of stdout — a ready-made input list for content analysis.

### Match a finicky target's request behavior

Get past UA or method restrictions while extracting.

```
getJS -url https://example.com -header "User-Agent: recon-agent" -timeout 15s
```

Outcome: fetches the page with the supplied header(s) and a 15-second timeout (`-method` switches to POST etc.), then prints extracted sources — useful when the default GET/UA is filtered or slow.

*No corpus evidence — derived from upstream documentation.*
