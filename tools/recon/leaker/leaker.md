---
name: "leaker"
categories:
  - recon
homepage: "https://github.com/vflame6/leaker"
github-repo: "vflame6/leaker"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: MIT
github-stars: 610
github-pushed: "2026-11-01"
github-commits-year: 179
gh-checked: "2026-11-14"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# leaker

> Passive credential-leak enumeration across 15 breach-intelligence sources — query by email, username, domain, keyword or phone, deduplicated, with verification.

## What it's for

leaker is a Go tool that turns one identifier (email, username, domain, keyword, phone number) into the credential leaks that exist for it, by querying passive online sources in parallel: fifteen breach databases including DeHashed, Intelligence X, LeakCheck, Leak-Lookup, Snusbase, WeLeakInfo, BreachDirectory, Hudson Rock and ProxyNova. Results are deduplicated across sources, cached in a local SQLite database, and can be emitted as JSONL for pipelines; `-V` additionally verifies found passwords against HIBP and identifies hash types. Most sources require API keys (several are paid; ProxyNova and Hudson Rock work without) configured via a provider config file, and the tool supports per-source rate limits, key rotation and HTTP proxying. In an engagement this is the external-recon step between "we have a domain" and "here are working credentials" — it never touches the target, everything it finds comes from third-party breach data.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Search all sources for one email

```
leaker email user@example.com
```

Outcome: queries every configured source for the address, deduplicates, and prints the leaks found (plaintexts/hashes, breach databases) on stdout.

### Pull every leak for a target domain

```
leaker domain example.com -j -o leaks.jsonl
```

Outcome: domain-wide search across sources, written as one JSON object per line to `leaks.jsonl` — the input file for credential-stuffing triage or reporting.

### See which sources are configured and available

```
leaker -L
```

Outcome: lists all supported sources with their search types — the check to run before wondering why a source returns nothing (most need API keys in the provider config).

### Verify leaked passwords

```
leaker email user@example.com -V
```

Outcome: for each leaked credential, runs the HIBP password check and hash identification so you can separate confirmed-current passwords from historical breach data.

### Route queries through a proxy

```
leaker username targetuser --proxy http://127.0.0.1:8080
```

Outcome: all source traffic goes through the HTTP proxy (chain with your operator setup); combine with `-A` for a custom User-Agent and `--insecure` only against MITM proxies that re-sign TLS.
