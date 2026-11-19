---
name: "httpscreenshot"
categories:
  - blackarch-misc
version: "70.f081c69"
homepage: "https://github.com/breenmachine/httpscreenshot"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "breenmachine/httpscreenshot"
github-stars: 649
github-pushed: "2024-10-01"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# httpscreenshot

> Mass-capture screenshots and HTML of an entire web attack surface.

*No corpus evidence — derived from upstream documentation.*

## What it's for

HTTPScreenshot (by breenmachine) grabs screenshots and page HTML from large numbers of websites, fast: it drives a real browser across a queue of URLs using a pool of worker threads, either headless (PhantomJS) or with a visible Firefox. Input is a plain URL list or nmap/masscan gnmap output, and the tool can grow its own target list by pulling hostnames out of SSL certificates (CN and SAN entries, plus brute-forced wildcard subdomains). The result is a set of per-host screenshots and HTML files that turns hundreds of barely-distinguishable web services into something a human can triage by eye. It is purely a capture/reconnaissance tool — no vulnerability testing — and its threading is aggressive enough that the author recommends a second, slower pass to recover timeout casualties.

## Typical use cases

### Screenshot a list of URLs headless

Bulk-capture from a simple URL list.

```
httpscreenshot -l urls.txt -p -w 10
```

Outcome: every reachable URL in `urls.txt` (one `http(s)://host` per line) is fetched by a headless browser across 10 worker threads; the page HTML and a screenshot image for each responding host are written to the current directory for manual review.

### Triage a port scan with autodetection and vhost discovery

Feed nmap/masscan output straight in — the author's recommended first pass.

```
httpscreenshot -i scan-output.gnmap -p -w 40 -a -vH
```

Outcome: services from the gnmap file are probed with HTTP/HTTPS autodetection (`-a` discards the scan's often-unreliable SSL hints), hostnames scraped from SSL certificates (`-vH`) join the URL queue, and 40 headless workers capture everything. Failures caused by over-threading are recovered by re-running with `-w 5`.

### Expand wildcard certificates by subdomain brute force

Turn `*.example.com` certificates into concrete hostnames to capture.

```
httpscreenshot -i scan-output.gnmap -p -vH -dB subdomains.txt
```

Outcome: for every wildcard certificate encountered during vhost scraping, each word in `subdomains.txt` is tried as a prefix; names that resolve join the URL queue and get screenshotted along with everything else. Only meaningful together with `-vH`.

### Capture through a SOCKS5 proxy

Route all browser traffic through a tunnel.

```
httpscreenshot -l urls.txt -p -pX 127.0.0.1:9050
```

Outcome: every fetch, including the headless browser's traffic, goes through the SOCKS5 proxy at `127.0.0.1:9050`, keeping the scanning host's real address off the target's logs.
