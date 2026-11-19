---
name: "certgraph"
categories:
  - blackarch-recon
version: "200.7c8dd0d"
homepage: "https://github.com/lanrat/certgraph"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "lanrat/certgraph"
github-stars: 384
github-pushed: "2026-10-11"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# certgraph

> Crawl the X.509 Subject Alternate Name graph to enumerate hosts and expose certificate reuse between domains.

## What it's for

CertGraph treats domains as nodes and a certificate's Subject Alternate Names as directed edges: starting from a seed host it fetches the certificate, discovers every SAN as a new node, and repeats breadth-first up to a configurable depth. Its primary use is hostname enumeration, but the same graph exposes "chains" of trust — organizations sharing certificates show up as connected components. Certificates are collected live over TCP with the default `http` driver (or `smtp`, which uses STARTTLS on port 25), or entirely passively from Certificate Transparency logs via the `crtsh` and `censys` drivers, which send no packets to the target domains at all.

## Typical use cases

### Enumerate hosts with certificate details

Get the full adjacency list for a seed domain.

```
certgraph -details example.com
```

Outcome: prints each newly discovered domain as it is found, then on completion the graph adjacency list in the form `Node  Depth  Status  Cert-Fingerprint`, showing which certificates link the discovered domains together.

### Enumerate passively from CT logs

Discover hosts without sending any traffic to the target.

```
certgraph -driver crtsh example.com
```

Outcome: enumerates SANs purely from crt.sh Certificate Transparency records — nothing connects to the target domain, which keeps the recon invisible to the asset owner (censys works the same way but needs API keys via -censys-appid/-censys-secret).

### Export the graph as JSON

Produce input for the bundled web UI.

```
certgraph -json example.com > graph.json
```

Outcome: writes the crawl graph as JSON, the format accepted by CertGraph's web interface; run `certgraph --serve 127.0.0.1:8080` and load the file there to explore the graph visually.

### Detect BygoneSSL exposure

Find third parties still trusting a certificate you share.

```
certgraph -depth 1 -driver crtsh -ct-subdomains -cdn -apex example.com
```

Outcome: with all of your own domains as input, any domain printed that you do NOT own is a third party reachable through your certificate — the BygoneSSL cross-tenant exposure; for bug-bounty hunting the passive variant `certgraph -cdn -dns -apex example.com` flags certificates whose domains no longer resolve (`* Missing DNS for`).

*No corpus evidence — derived from upstream documentation.*
