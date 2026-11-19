---
name: "domained"
categories:
  - blackarch-recon
version: "80.d9d079c"
homepage: "https://github.com/TypeError/domained"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "TypeError/domained"
github-stars: 720
github-pushed: "2021-04-11"
github-archived: true
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# domained

> Orchestrator for subdomain recon: drives a stack of third-party enumeration tools, merges their findings, and hands the list to EyeWitness for screenshot reporting.

## What it's for

domained wraps several external subdomain enumerators (Sublist3r, enumall, Knock, Subbrute, massdns, Amass, SubFinder) behind one command, deduplicates their output into a single host list, and can pass that list to EyeWitness to capture categorized screenshots, server response headers and default-credential checks for every web host found. It was built as a one-shot, Kali-oriented bug-bounty recon driver; the upstream repo is archived and unmaintained, and because it manages its companion tools itself the dependency chain is heavyweight. Output lands under `./output`, fetched tools under `./bin`. It adds no enumeration logic of its own — its value is the orchestration and the merged result.

## Typical use cases

### Run the full enumeration stack

```
domained -d example.com
```

Outcome: runs Sublist3r (with Subbrute), enumall, Knock, Amass and SubFinder against example.com, merges the results, and launches EyeWitness over the merged list; artifacts appear under `./output`.

### Add bruteforcing and extra ports

```
domained -d example.com -b -p --vpn
```

Outcome: `-b` adds subbrute/massdns bruteforcing with a SecLists wordlist, `-p` adds ports 8080/8443 to what EyeWitness probes, and `--vpn` makes it refuse to run without a VPN interface up — a safety catch for engagement work.

### Fast pass with only two tools

```
domained -d example.com --quick
```

Outcome: skips the slower enumerators and runs only Amass and SubFinder — the usual speed-versus-coverage trade-off for large scopes.

### Enumeration only, no screenshots

```
domained -d example.com --noeyewitness
```

Outcome: produces the merged subdomain list but never starts EyeWitness, leaving browser-based reporting out of the run.

*No corpus evidence — derived from upstream documentation.*
