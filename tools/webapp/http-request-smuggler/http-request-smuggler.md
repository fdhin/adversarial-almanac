---
name: "http-request-smuggler"
categories:
  - webapp
homepage: "https://github.com/portswigger/http-request-smuggler"
origin: "pentest-book"
recipe: "upstream"
packaged-by: []
build-system: java
github-repo: "portswigger/http-request-smuggler"
github-stars: 1251
github-match: "exact"
gh-checked: "2026-11-13"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
license: "see repository"
---

# http-request-smuggler

> Burp Suite extension that automatically detects and exploits HTTP request smuggling (desync) vulnerabilities.

## What it's for

HTTP Request Smuggler (PortSwigger, built on the desynchronization research of James Kettle) automates finding and exploiting HTTP request smuggling: it probes a target with desync techniques and reports when a front-end/back-end parser disagreement lets you smuggle a second request through an established connection. Coverage is broad — classic HTTP/1.1 CL.TE and TE.CL desyncs confirmed by timeout, HTTP/2 request smuggling including downgrade tunneling and header injection, client-side desyncs that run in a victim's browser, header smuggling/removal, and connection-state/pause-based techniques — and version 3.0 (2025) adds root-cause parser-discrepancy detection that bypasses widespread desync defenses, per the "HTTP/1.1 Must Die" whitepaper. Detection is root-cause based rather than symptom based, which upstream reports makes it more reliable against target-specific quirks, with permutation techniques to get past different server configurations and multiple validation passes to cut false positives. It runs in Burp Suite DAST, Professional and Community; Pro and Community add a "research mode" for exploring novel techniques. It needs a request to start from — it is not a crawler, and a clean probe result on one endpoint does not clear the whole application.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Probe a request for desync vulnerabilities

Start from any interesting request seen in the proxy.

```
Burp: right-click a request -> "Launch Smuggle probe" -> OK
watch: Extender -> Extensions -> HTTP Request Smuggler (output pane)
```

Outcome: the extension fires its desync probe suite at the target and logs progress in its output pane ("Completed 1 of 1" signals the probe finished); in Burp Pro any confirmed smuggling condition is also raised as a scan issue in the dashboard with the offending requests attached, while Community users copy the probe requests from the output pane into Repeater to confirm manually.

### Exploit a confirmed desync with a smuggled prefix

Turn a detected desync into an actual smuggled request.

```
Burp: right-click a chunked request -> "Launch Smuggle attack"
Turbo Intruder window: edit the "prefix" variable (e.g. set it to G) -> Attack
```

Outcome: opens a Turbo Intruder window pre-loaded with an attack template — editing the `prefix` variable changes what gets prepended to the next victim request; in the upstream walkthrough, a prefix of `G` makes the back-end read the smuggled text as the method of the next request and answer `Unrecognised method GPOST`, proving the smuggle. Upstream notes that varying `prefix` covers virtually every real-world smuggling scenario (capturing other users' requests, endpoint poisoning, cache poisoning).

### Keep watching for novel desyncs

Fold smuggling detection into routine scanning.

```
Burp DAST: recurring scans flag novel threats as new techniques are released
Burp Pro/Community: HTTP Request Smuggler settings -> research mode
```

Outcome: the DAST integration re-runs detection as part of scheduled scans so newly released techniques get flagged on the next run, while research mode (Pro/Community) enables experimental desync techniques beyond the stable set — useful when a target is patched against the well-known probe types.
