---
name: "dnscobra"
categories:
  - blackarch-recon
version: "1.0"
homepage: "https://github.com/dmitescu/dnscobra"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "dmitescu/dnscobra"
github-stars: 0
github-pushed: "2020-04-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnscobra

> DNS subdomain bruteforcer built to run under torsocks so enumeration queries don't leak outside Tor.

## What it's for

dnscobra is a dictionary-based subdomain brute-forcer whose distinguishing
feature is how it resolves: it performs lookups through `getaddrinfo`
specifically so that torsocks can proxy every DNS request through the Tor
network. Its reason to exist is the standard enumeration tooling's habit of
leaking your real resolver identity (and thus your location/attribute) even
when the rest of your traffic is Tor-routed — dnscobra keeps the entire
enumeration inside the circuit. Bring your own dictionary (the README points
at the dnsrecon wordlist as a good one); results can be written to CSV. Only
run it against assets you are authorized to enumerate, and remember Tor exit
traffic is attributable to the exit node, not a license for abuse.

## Typical use cases

### Brute subdomains through Tor

The intended mode: enumeration without DNS leakage.

```
torsocks dnscobra --threads 3 -D subdomains.txt -o found.csv example.com
```

Outcome: with the Tor daemon running, every resolution for the dictionary
entries leaves through the Tor circuit; subdomains that resolve are written
to found.csv. Check the torsocks output at startup to confirm the circuit
was engaged before queries began.

### Brute subdomains directly

Same sweep when DNS leakage is not a concern.

```
dnscobra --threads 3 -D subdomains.txt -o found.csv example.com
```

Outcome: identical enumeration with queries going out over the normal
resolver path — faster and cleaner than the Tor path, at the cost of
exposing the querying host's resolver identity to the target's
infrastructure.

No corpus evidence — derived from upstream documentation.
