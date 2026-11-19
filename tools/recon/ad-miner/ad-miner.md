---
name: "ad-miner"
categories:
  - blackarch-recon
version: "v1.9.0.r0.gac3d473"
homepage: "https://github.com/AD-Security/AD_Miner"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-03-28"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "AD-Security/AD_Miner"
github-stars: 1585
github-pushed: "2026-03-18"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 20
gh-activity-checked: "2026-11-09"
---

# ad-miner

> Audits an Active Directory (or Entra ID) by running Cypher queries against a BloodHound Neo4j database and rendering the weaknesses as a static, risk-rated web report.

## What it's for

AD Miner is an Active Directory auditing tool for the analysis phase of an assessment: it assumes you have already collected domain data into a BloodHound graph database (with SharpHound, BloodHound.py, RustHound or AzureHound) and runs a battery of Cypher queries against Neo4j to surface weaknesses — privilege paths, dangerous ACLs, stale objects, password-policy gaps and more. Results land in a static HTML report with detailed listings, dynamic graphs, risk ratings to prioritize fixes, and key-indicator history when you keep successive reports. It does not collect from the domain itself and does not attack anything; it turns a BloodHound dataset into an auditor-friendly report, on-premise AD and Entra ID both.

## Typical use cases

### Generate a report from a local BloodHound database

Run the standard audit against Neo4j on the default bolt port.

```
AD-miner -cf My_Report -u neo4j -p Password1
```

Outcome: queries the Neo4j instance at `bolt://127.0.0.1:7687`, computes all controls, and writes a static report directory (`My_Report`) with an `index.html` you open in a browser — risk ratings, per-control listings, and interactive graphs. Intermediate Cypher results are cached under `cache_neo4j/`, so `-c` on a later run reuses them instead of re-querying.

### Point at a remote Neo4j and set the password-renewal policy

Audit a BloodHound database hosted elsewhere, with domain-accurate staleness math.

```
AD-miner -cf My_Report -b bolt://198.51.100.10:7687 -u neo4j -p Password1 -r 180
```

Outcome: connects to the remote bolt endpoint and evaluates password-age controls against a 180-day renewal policy (default 90) so "password never changed" style findings match the environment's actual policy instead of flagging compliant accounts.

### Speed up large domains with parallel queries

Spread the Cypher workload across cores and several Neo4j nodes.

```
AD-miner -c -cf My_Report -u neo4j -p Password1 --cluster server1:7687:32,server2:7687:16
```

Outcome: Neo4j requests are chunked and executed in parallel (32 threads on server1, 16 on server2), materially shortening runtime on large datasets; `-co`/`-ch` tune core and chunk counts when no cluster is used.

### Track the attack surface over time

Compare successive reports to see whether the domain is getting better or worse.

```
AD-miner -cf Q4_Report -u neo4j -p Password1 --evolution ../reports-history/
```

Outcome: each report also emits a JSON data file; pointing `--evolution` at a folder collecting those files makes the report render historical key-indicator curves — password-stale counts, admin-path counts and similar metrics per period, giving a trend line across quarters.

*No corpus evidence — derived from upstream documentation.*
