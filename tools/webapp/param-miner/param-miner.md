---
name: "param-miner"
categories:
  - webapp
homepage: "https://github.com/PortSwigger/param-miner"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: java
license: see repository
meta-checked: "2026-11-13"
github-repo: "PortSwigger/param-miner"
github-match: "exact"
github-stars: 1465
gh-checked: "2026-11-13"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# param-miner

> Burp Suite extension that brute-forces hidden parameters, headers and cookie names — the standard tool for finding web cache poisoning inputs.

## What it's for

Param Miner is a Burp Suite extension by PortSwigger (James Kettle) that
identifies hidden, unlinked parameters — URL/query parameters, HTTP
headers and cookies that an application or intermediary reacts to without
advertising them. It combines the diffing logic of Backslash Powered
Scanner with a binary-search guess of up to ~65,000 names per request from
a curated built-in wordlist (plus words harvested from all in-scope
traffic). Its signature use is web cache poisoning research — discovering
the unkeyed header or parameter a cache will store and serve to others.
There is no standalone CLI: it is operated from inside Burp, so every use
case is a UI workflow.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Guess hidden parameters on one request

Find unkeyed/unlinked GET parameters.

```
Burp Proxy/Repeater -> right-click a request -> Guess -> params
```

Outcome: re-issues the request with batches of candidate parameter names
and diffs the responses; on Burp Pro, discovered parameters are raised as
scanner issues, otherwise they appear under
Extender -> Extensions -> Param Miner -> Output.

### Guess hidden headers

Hunt for unkeyed headers that change the response (cache poisoning).

```
Burp Proxy/Repeater -> right-click a request -> Guess -> headers
```

Outcome: same binary-search + diff flow against header names; a header
that meaningfully alters the response is reported — the classic entry
point to cache-poisoning investigation.

### Guess cookie names

Check for cookies the application silently honors.

```
Burp Proxy/Repeater -> right-click a request -> Guess -> cookies
```

Outcome: candidate cookie names are injected and response differences
reported through the same output channel as params/headers.

### Bulk-guess across many requests

Scale the sweep to a whole sitemap selection.

```
Burp -> select multiple requests -> right-click -> Guess -> (params|headers|cookies)
```

Outcome: guessing runs across every selected request on a thread pool —
safe to point at thousands of requests; findings accumulate in the
extension output / scanner issues.

### Mine all in-scope traffic automatically

Let the extension harvest passively during a whole test.

```
Burp -> Extender -> Extensions -> Param Miner -> Options -> enable auto-mining of in-scope traffic
```

Outcome: every in-scope request gets mined as traffic flows; the
extension is built to scale but the options (wordlist size, threads) may
need tuning to avoid performance problems. A custom wordlist can be
supplied in the same options panel, and the Distribute Damage extension
can rate-limit the attack traffic.

### Build and load the extension manually

Use the latest source instead of the BApps release.

```
./gradlew build fatjar   ->  Burp -> Extender -> Extensions -> Add -> load build/libs/param-miner-all.jar
```

Outcome: produces `param-miner-all.jar` and loads it into Burp; requires
Burp Suite 2021.9 or later (the normal install path is the BApps tab).
