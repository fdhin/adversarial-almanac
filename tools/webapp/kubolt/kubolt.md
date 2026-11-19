---
name: "kubolt"
categories:
  - blackarch-webapp
version: "28.0027239"
homepage: "https://github.com/averonesis/kubolt"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "averonesis/kubolt"
github-stars: 107
github-pushed: "2026-01-09"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kubolt

> OSINT utility that asks Shodan for internet-exposed Kubernetes kubelets, then verifies which are unauthenticated and proves command execution inside their containers.

## What it's for

kubolt chains two ideas into one script: Shodan knows where kubelet port 10250 answers (`ssl:true port:10250 404` is the base query), and a kubelet left without authentication exposes `/runningpods/` and a `/run/<namespace>/<pod>/<container>` endpoint that executes commands in containers. The tool takes a Shodan query (narrowed by `org`, `asn`, `country` or `net` filters), collects result IPs, drops the ones answering `Unauthorized`, and for the remainder runs `id` in each container via the run endpoint, printing the output. It is a point-and-shoot auditor for accidental kubelet exposure across an organization's external footprint. Two caveats: it consumes Shodan query credits (an unfiltered query burns them fast), and the Shodan API key must be pasted into the script's `API_KEY` constant before use. Verification only — what you do with a confirmed exposure is your remediation or authorization decision.

## Typical use cases

### Sweep an organization for exposed kubelets

The canonical run.

```
kubolt --query "org:'Example Corp'"
```

Outcome: Shodan is queried for that organization's kubelet-port hosts; each IP found is checked for unauthenticated `/runningpods/`, and where pods are visible the script POSTs `cmd=id` to the run endpoint per container and prints the `uid/gid` output — direct evidence of command execution on every confirmed cluster.

### Narrow the hunt with network attributes

Target by ASN, country or netblock.

```
kubolt --query "asn:64512 country:'GB'"
```

Outcome: the same pipeline restricted to the given network attributes, which both focuses the sweep and spends fewer Shodan query credits than the broad default query; the base query can also be extended with any Shodan filter the API supports.

No corpus evidence — derived from upstream documentation (averonesis/kubolt README and `kubolt.py` source: `--query` argparse, Shodan `search_cursor`, hardcoded `cmd=id` POST to `:10250/run/`).
