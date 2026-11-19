---
name: "cloudget"
categories:
  - blackarch-webapp
version: "64.cba10b1"
homepage: "https://github.com/eudemonics/cloudget"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "eudemonics/cloudget"
github-stars: 19
github-pushed: "2020-09-01"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cloudget

> Command-line fetcher that solves Cloudflare's anti-bot interstitial before retrieving a page.

## What it's for

cloudget wraps the cfscrape library to fetch content from sites that sit behind Cloudflare's JavaScript challenge: it clears the challenge, then optionally harvests links or saves the response. It is a retrieval helper for reconnaissance and scraping workflows, not an attack tool — reach for it when `curl` gets the Cloudflare block page. The URL must be given complete and starting with `http://` or the script will not target it correctly. The packaged version runs on the legacy Python 2 stack its cfscrape dependency requires, so it tracks old Cloudflare behavior and may fail against modern challenges.

## Typical use cases

### Fetch a page behind Cloudflare

Retrieve the challenged content to stdout or a file.

```
cloudget -u http://target.example.com/ -o page.html
```

Outcome: solves the Cloudflare interstitial, downloads the page, and writes it to `page.html`; without `-o` the body prints to the terminal. Use the full URL including the scheme.

### Harvest all links from a protected page

Collect every link in the cleared response.

```
cloudget -u http://target.example.com/ -l
```

Outcome: after clearing the challenge, parses the response and prints every link found in it — a quick way to enumerate URLs on a site that blocks plain HTTP clients.

### Route the fetch through a proxy

Send the challenge-solving traffic through a local intercepting proxy.

```
cloudget -u http://target.example.com/ -p http://127.0.0.1:8080
```

Outcome: performs all requests through the given HTTP proxy, so the Cloudflare handshake and the final retrieval are visible in the proxy's history.

No corpus evidence — derived from upstream documentation.
