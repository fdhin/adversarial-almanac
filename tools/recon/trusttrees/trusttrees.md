---
name: "trusttrees"
categories:
  - blackarch-recon
version: "102.a9b7399"
homepage: "https://github.com/mandatoryprogrammer/TrustTrees"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mandatoryprogrammer/TrustTrees"
github-stars: 406
github-pushed: "2023-05-22"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# trusttrees

> Recursively graph a domain's DNS delegation chain (NS trust tree) and flag delegation errors and expired, registrable base domains that enable hijacking.

## What it's for

TrustTrees (by Yan, @mandatoryprogrammer) recursively follows every delegation path for a target domain — root servers down through TLD servers to the authoritative nameservers — and renders the nameserver relationships as a Graphviz graph. Its purpose is DNS hygiene and takeover detection: the graph exposes broken delegation links (REFUSED, NXDOMAIN and other errors, dead-end nameservers with no IP) and, when given registrar API credentials, it checks whether the base domains of enumerated nameservers have expired and are registerable — meaning anyone can register them and hijack the delegation. Domain owners use it to verify their DNS is set up properly; assessors use it to find sibling/expired-domain takeover paths. It only queries DNS and registrar availability APIs; it does not attempt the hijack itself.

## Typical use cases

### Graph a domain's delegation tree

Visualize how a domain's NS records chain together.

```
trusttrees --target example.com --open
```

Outcome: walks the delegation chain from the root servers down, printing each nameserver queried, builds the graph (white nodes = non-authoritative referrers, blue = authoritative answers, red = nameservers with no IP, yellow = DNS errors), saves it as an image and `-o/--open` opens it in your viewer.

### Audit many domains from a list

Process a batch of hostnames in one run.

```
trusttrees --target-list targets.txt --only-problematic
```

Outcome: one graph per hostname in the file, with `--only-problematic` restricting generation to domains whose trees show likely-vulnerable delegation paths — the quick triage mode for large inventories.

### Export graphs for a report

Produce specific formats without opening viewers.

```
trusttrees --target example.com -x png,pdf
```

Outcome: writes the delegation graph in each requested format (e.g. graph.png and graph.pdf) next to the run instead of only rendering interactively; `--no-graphing` skips graph generation entirely when you only want the console findings.

### Check nameserver base domains for takeover

Use a registrar API to find expired, registrable delegation parents.

```
trusttrees --target example.com --gandi-api-v5-key <KEY>
```

Outcome: for each nameserver encountered, its base domain is checked for availability; registrable ones appear as orange "domain unregistered" nodes in the graph — the register-and-hijack condition. Equivalent checks work via `--aws-credentials` (needs `route53domains:CheckDomainAvailability`) or `--dnsimple-api-v2-token`; custom resolvers can be supplied with `--resolvers resolvers.txt`.

*No corpus evidence — derived from upstream documentation.*
