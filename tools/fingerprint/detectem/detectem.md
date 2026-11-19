---
name: "detectem"
categories:
  - blackarch-fingerprint
version: "276.bc5f073"
homepage: "https://github.com/spectresearch/detectem"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "spectresearch/detectem"
github-stars: 156
github-pushed: "2021-03-25"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# detectem

> Docker-based web technology detector — renders a site in a headless Splash browser and identifies the software (with exact versions) from requests, responses and the rendered DOM.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Modern web-stack fingerprinting: which CMS, forum engine, JS library or server module a site runs, at version precision. detectem drives a Splash headless-browser container in Docker so it observes what a real browser observes, then applies a plugin system whose indicators run over requests made, responses received and the DOM itself — catching client-side and dynamically loaded software that plain fetch-and-grep misses. It requires Docker with the Splash image (`scrapinghub/splash`) available; without the Splash container it cannot run. Results print to stdout as text or JSON, for a single URL or a batch from a file. Python 3.6+; the pip package installs the entry point as `det` (packaged on BlackArch as `detectem`). Development stopped around 2021, so recent software may lack detection plugins.

## Typical use cases

### Detect the software stack of a site

Fingerprint one URL end to end.

```
detectem http://target.example.com
```

Outcome: renders the page through Splash and prints the detected software with versions, e.g. `[{'name': 'jquery', 'version': '1.11.3'}, {'name': 'apache-mod_bwlimited', 'version': '1.4'}]` — typically in under ten seconds.

### Get machine-readable output

Produce JSON for feeding other tooling.

```
detectem --format json http://target.example.com
```

Outcome: prints the same detections as a JSON document; `--metadata` adds each plugin's metadata to the records.

### Fingerprint many sites from a file

Batch a crawl or assessment scope in one run.

```
detectem -i urls.txt
```

Outcome: reads one URL per line from urls.txt, fingerprints each in turn, and reports detections per site, with per-site errors surfaced on stderr.

### List available detection plugins

See what this build can recognize.

```
detectem --list-plugins
```

Outcome: prints every registered software-detection plugin, showing which products are covered before you scan.
