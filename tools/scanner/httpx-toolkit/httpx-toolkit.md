---
name: "httpx-toolkit"
categories:
  - scanner
version: "1.7.4-0parrot2"
homepage: "https://github.com/projectdiscovery/httpx"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/httpx"
github-stars: 10592
github-pushed: "2026-11-06"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 358
gh-activity-checked: "2026-11-09"
---

# httpx-toolkit

> ProjectDiscovery's fast multi-purpose HTTP probing toolkit: pipe in hosts, URLs or CIDRs and get back the live web servers with status, title, technology, TLS and dozens more probes per host.

*No corpus evidence — derived from upstream documentation (projectdiscovery/httpx README; flags verified against the README's embedded help text and tested recipes). Not to be confused with the Ruby `httpx` HTTP client library, which has its own note under webapp/httpx.*

## What it's for

httpx is the standard liveness-and-metadata stage in a modern recon pipeline. It accepts hosts, URLs and CIDR ranges on stdin or via `-l`, probes them concurrently with retry/backoff handling (via retryablehttp, which also copes with WAFs), auto-falls back between HTTPS and HTTP, and emits one line (or one JSONL record) per responding host enriched with any of its ~25 probes: status code, title, web server, technology detection (wappalyzer dataset), content length, word/line counts, TLS certificate details, CNAME, CDN detection, virtual hosts, redirect chains, favicon hashes and more. It replaces ad-hoc `curl` loops over huge host lists; it does not crawl content or find parameters — downstream tools do that with its output. The vault package name `httpx-toolkit` (Parrot) ships the `httpx` binary.

## Typical use cases

### Turn a host list into a live-web inventory with metadata

```
cat hosts.txt | httpx -sc -title -td
```

Outcome: Prints one line per responding host with its URL, status code (`-sc`), page title (`-title`) and detected technology stack (`-td`, wappalyzer-based) — a sorted, greppable snapshot of what is actually running.

### Probe alternate ports across a domain set

```
cat subdomains.txt | httpx -p 80,443,8080,8443 -title
```

Outcome: Each hostname is additionally probed on the listed ports (`-p`, nmap-style syntax supported), so services on management or alternate ports appear in the same output stream.

### Discover well-known files with content validation

```
echo target.example.com | httpx -path '/.well-known/security.txt,/security.txt' -mc 200 -mdc 'contains(content_type, "text/plain") && contains(body, "Contact:")'
```

Outcome: Probes the listed paths (`-path`), keeps only HTTP 200 responses (`-mc`), and applies the DSL matcher (`-mdc`) to content type and body — the host prints only if a genuinely parseable security.txt came back. The same pattern works for robots.txt, sitemap.xml, ads.txt and OIDC discovery documents.

### Capture a structured snapshot for later diffing

```
cat hosts.txt | httpx -j -fr -silent > httpx-snapshot.jsonl
```

Outcome: Writes one JSON object per live host (`-j`, JSONL) including followed-redirect end states (`-fr`), suppressing the banner (`-silent`); re-running periodically and diffing the file shows new hosts, dead hosts and changed titles or technologies.
