---
name: "wcvs"
categories:
  - blackarch-webapp
version: "2.0.0.r2.g75159ea"
homepage: "https://github.com/Hackmanit/Web-Cache-Vulnerability-Scanner"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-31"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "Hackmanit/Web-Cache-Vulnerability-Scanner"
github-stars: 1211
github-pushed: "2026-01-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# wcvs

> Go CLI scanner for web cache poisoning and web cache deception by Hackmanit.

## What it's for

WCVS tests a target for ten web cache poisoning techniques — unkeyed header and parameter poisoning, parameter cloaking, Fat GET, HTTP response splitting, HTTP request smuggling, HTTP header oversize (HHO), HTTP meta character (HMC), HTTP method override (HMO), and parameter pollution — plus web cache deception variants (path parameter, path traversal toward `.css`/`/static`/`/robots.txt`, appended special characters). It analyzes the cache before testing and adapts its probes to it, can crawl the site for further URLs, writes JSON reports, routes traffic through a proxy, and rate-limits itself to survive rate limiting. The first five poisoning techniques need a header-name and a parameter-name wordlist (shipped defaults are picked up from the working directory, or set them with `-hw`/`-pw`). It is CI/CD-friendly: single static binary, exit-code and report driven.

## Typical use cases

### Test one URL for cache vulnerabilities

Quick assessment of a single endpoint.

```
wcvs -u https://example.com
```

Outcome: runs all supported poisoning and deception techniques against the URL, printing each confirmed finding; header and parameter wordlists are read from the current directory unless overridden.

### Supply wordlists explicitly

Point at dedicated wordlists for header and parameter fuzzing.

```
wcvs -u https://example.com -hw "file:wordlist-header.txt" -pw "file:wordlist-parameter.txt"
```

Outcome: the `file:` prefix loads multi-value wordlists from disk, driving the unkeyed header/parameter, cloaking, Fat GET, response splitting, and smuggling checks.

### Add cookies, headers, and parameters

Reach authenticated or state-dependent cache behavior.

```
wcvs -u https://example.com -sc "PHPSESSID=123" -sh "Referer: localhost" -sp "admin=true"
```

Outcome: every probe carries the given cookie, header, and parameter; each flag also accepts `file:` lists for multiples, and `-post` with `-sb` switches requests to POST with a body.

### Generate a JSON report

Keep machine-readable evidence of the scan.

```
wcvs -u https://example.com -gr -gp ./wcvs-out
```

Outcome: writes and rewrites a JSON report into `./wcvs-out` after each scanned URL; add `-ej` to HTML-encode special characters in it.

### Crawl for more URLs

Expand the scan beyond the starting URL.

```
wcvs -u https://example.com -r 2
```

Outcome: crawls the site to recursion depth 2 (same domain by default) and tests every URL found; `-red` widens to a domain list, `-rex` excludes URLs, and `-rl` caps URLs per depth.

### Route through an intercepting proxy

Watch and debug the probes in Burp.

```
wcvs -u https://example.com -up -purl http://127.0.0.1:8080
```

Outcome: all requests are sent through the proxy (default `http://127.0.0.1:8080`); disable Burp's "Default to HTTP/2" setting so non-RFC-compliant headers used by some techniques survive. `-rr 5` independently caps the scan at five requests per second.

*No corpus evidence — derived from upstream documentation (Hackmanit/Web-Cache-Vulnerability-Scanner README).*
