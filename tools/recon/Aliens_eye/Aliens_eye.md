---
name: "Aliens_eye"
categories:
  - recon
version: "git (2026-11-07)"
homepage: "https://github.com/arxhr007/Aliens_eye"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "arxhr007/Aliens_eye"
github-stars: 4794
github-pushed: "2026-11-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 77
gh-activity-checked: "2026-11-09"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# Aliens_eye

> AI-assisted OSINT username scanner that checks a handle across thousands of social platforms and clusters the hits into one person's footprint.

## What it's for

Aliens Eye takes a username (or several) and checks whether an account with that handle exists on a large catalog of social-media and web platforms — the extended default catalog covers thousands of sites, with a smaller standard catalog for quick scans. Instead of trusting bare HTTP status codes, it blends a trained ML model with structural signals (DOM shape, keywords, fingerprints) and a negative-control check against an impossible username, so false "found" hits get downgraded to "maybe". It also extracts profile data (display name, bio, avatar), can correlate hits across sites into likely-same-person clusters, and exports reports in JSON, CSV, HTML, Markdown, PDF, and graph formats. It is a passive OSINT collector: it only reads public profile pages, never attempts logins.

## Typical use cases

### Scan one username across the default catalog

Get every platform where the handle appears to exist.

```
aliens_eye username
```

Outcome: interactive prompts (or direct scan) produce a live progress display and a sorted results table plus a report file under `results/`; each hit is marked Found or Maybe with its URL, and the summary panel shows per-status counts.

### Restrict or shape the scan surface

Limit scanning to specific sites and skip NSFW platforms, or switch to the smaller catalog for speed.

```
aliens_eye username --site github,reddit,gitlab --no-nsfw --catalog standard
```

Outcome: only the named sites are queried from the standard (few-hundred-site) catalog, giving a much faster scan with fewer requests; results are formatted identically to a full scan.

### Route the scan through Tor or a proxy

Avoid tying your source IP to username enumeration.

```
aliens_eye username --tor
```

Outcome: all platform probes exit through the local Tor daemon (or through `--proxy socks5://127.0.0.1:1080` for any HTTP/SOCKS proxy); the scan otherwise behaves the same.

### Correlate hits into one identity

Cluster profiles that look like the same person and check whether the handle is registered as a domain.

```
aliens_eye username --correlate --domains
```

Outcome: hits are grouped into "likely same person" clusters using avatar hashes, bios, shared links and names, and `<username>.com/.io/.net/...` registration status is appended to the report — a ready-made identity map for further OSINT.

### Machine-readable output for scripts

Emit only the JSON report on stdout so pipelines can consume it.

```
aliens_eye username --json-stdout --only-found | jq '.variations[].sites | to_entries[] | select(.value.status == "Found") | .value.url'
```

Outcome: one JSON line per scanned username on stdout (banner and progress go to stderr); the jq filter leaves just the confirmed profile URLs, suitable for feeding another tool.

### Watch a handle over time

Re-scan on a schedule and be notified when the footprint changes.

```
aliens_eye username --watch 6h --notify https://webhook.example.com/hook
```

Outcome: the scan repeats every six hours and any new or changed profiles are POSTed to the webhook — useful for monitoring a handle's appearance on new platforms.

*No corpus evidence — derived from upstream documentation.*
