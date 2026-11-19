---
name: "davoset"
categories:
  - blackarch-dos
version: "1.3.7"
homepage: "http://websecurity.com.ua/davoset/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "MustLive/DAVOSET"
github-stars: 126
github-pushed: "2018-12-19"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# davoset

> DAVOSET — Perl tool that turns other sites' Abuse-of-Functionality and XXE flaws into a distributed flood aimed at one target site.

## What it's for

DAVOSET ("DDoS Attacks via Other Sites Execution Tool", by MustLive of websecurity.com.ua) performs denial-of-service attacks against one site by abusing vulnerable third-party web sites as intermediaries: services that fetch attacker-supplied URLs (Abuse of Functionality) or resolve external entities in submitted XML (XXE) are tricked into sending their requests at the chosen target. The traffic therefore arrives from many legitimate services and cannot be blocked by simple IP filtering. The tool ships with a ~240-entry list of known usable "zombie servers" (`list_full.txt`) plus per-service definition files, and supports standard one-pass mode, cyclic mode with a cycle count, cache bypass, logging, and routing its own requests through SOCKS or Tor. It is a research/testing tool from 2010-2018; many listed services will have been fixed since.

### Attack a target site through the bundled zombie-server list

The default one-pass run: every known usable service is made to request the target.

```
./davoset.pl u=http://target.example.com/
```

Outcome: reads `list_full.txt`, triggers each listed service against the target URL in turn, prints the working process and a final statistic of how many zombie servers participated, and appends results to `logs.txt`. Without `u=` on the command line the tool interactively prompts for the target site.

### Check which zombie servers are still usable

Prune the list before a test run — entries rot over time.

```
./davoset.pl test
```

Outcome: skips attacking a target and instead runs the bundled test against a control URL, reporting which entries in the server list still respond so a stale list can be cleaned up before the real run.

### Run a sustained cyclic attack through Tor

Cyclic mode repeats the whole list, and Tor hides the tool's own origin.

```
./davoset.pl u=http://target.example.com/ l=list.txt m=1 c=500 p=2
```

Outcome: cycles through the custom server list in `list.txt` up to 500 times (capped at the built-in maximum of 2000), sending the tool's own requests through Tor at 127.0.0.1:9051 (`p=1` would use a SOCKS proxy instead); each cycle's results are logged. Other useful switches: `b=1` cache bypass, `log=0` to disable logging.

*No corpus evidence — derived from upstream documentation.*
