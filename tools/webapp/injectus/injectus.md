---
name: "injectus"
categories:
  - blackarch-webapp
version: "12.3c01fa0"
homepage: "https://github.com/BountyStrike/Injectus"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BountyStrike/Injectus"
github-stars: 111
github-pushed: "2021-08-31"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# injectus

> Fuzzes URLs with CRLF-injection and open-redirect payloads, injecting each payload into every parameter in turn.

## What it's for

Injectus (by @dubs3c, part of the BountyStrike bug-bounty tooling) takes a URL or a file of URLs and systematically substitutes every CRLF and open-redirect payload into the value of each parameter — so `?a=1&b=2` gets each payload tried against `a` and against `b` — firing the requests with asyncio workers and reporting as it goes. Built as a pipeline stage to feed it every URL you have and let it grind the two vulnerability classes at once. It can also only build the attack list without sending anything (`--no-request`), or restrict a run to one class. The upstream repository is archived — the tool still runs but is unmaintained; the packaged `injectus` command wraps Injectus.py.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fuzz a single URL for both vulnerability classes

The one-argument full run.

```
injectus -u "https://example.com/?page=profile&redirect=https://example.com/home"
```

Outcome: every parameter of the URL is tested with the full CRLF and open-redirect payload sets using the defaults (10 asyncio workers, 6-second timeout, 1-second delay), with findings printed as they occur.

### Build the attack list without sending

Preview or reuse the generated payloads.

```
injectus -u "https://example.com/?q=test" -r
```

Outcome: prints every payload-injected URL variant for the target without performing any requests — the list can be reviewed or piped to other tooling.

### Fuzz a URL list, one class, throttled

Grind large recon files gently.

```
injectus -f urls.txt -c -w 5 -d 2
```

Outcome: only CRLF payloads (`-c`; `-op` is the open-redirect-only counterpart) are tried across every URL in `urls.txt`, with 5 workers and a 2-second delay between requests.
