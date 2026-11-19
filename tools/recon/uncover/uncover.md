---
name: "uncover"
categories:
  - blackarch-recon
version: "v1.0.2.r2.g4b929e0"
homepage: "https://github.com/projectdiscovery/uncover"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2023-02-20"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/uncover"
github-stars: 3104
github-pushed: "2026-11-04"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 48
gh-activity-checked: "2026-11-09"
---

# uncover

> Query the APIs of many internet-wide search engines (Shodan, Censys, FOFA, ZoomEye, Quake, Netlas, ...) at once for exposed hosts, with pipe-friendly output.

## What it's for

uncover (by projectdiscovery) is a Go CLI that wraps the APIs of internet-wide asset search engines so one query fans out to many engines and returns a unified list of exposed host:port results. Supported engines include shodan, shodan-idb, fofa, censys, quake, hunter, zoomeye, netlas, publicwww, criminalip, hunterhow, google, odin, binaryedge, onyphe, driftnet, greynoise, daydaymap and nerdydata. It is built for automation: queries can arrive on stdin, from files or per-engine flags; results go to stdout as `ip:port` lines (or JSONL), ready to pipe into httpx, naabu or a reporting pipeline. API keys are mandatory and live in `provider-config.yaml` (or environment variables); multiple keys per engine are randomized across requests. It searches — it never touches the discovered hosts itself.

## Typical use cases

### Query the default engine from stdin

One-liner exposure check through Shodan.

```
echo 'ssl:"Example Corp"' | uncover
```

Outcome: runs the query against shodan (the default engine) and prints discovered exposed endpoints as ip:port lines on stdout — directly pipeable into other tools.

### Run a file of queries in one shot

Batch multiple dorks/queries through the pipeline.

```
uncover -q queries.txt
```

Outcome: every line of queries.txt (one search query per line) is executed and all results are merged into the output stream — the bulk mode for building an external-asset inventory.

### Fan one query out to many engines

Cross-check coverage across search engines.

```
uncover -q jira -e shodan,censys,fofa,zoomeye -l 500
```

Outcome: the same query runs against each listed engine (engine-native syntax applies per engine) with up to 500 results per source, deduplicated into one host list — use when different engines index different regions or protocols.

### Give each engine its native query syntax

Express per-engine queries precisely.

```
uncover -shodan 'http.component:"Atlassian Jira"' -censys 'services.software.product=`Jira`' -fofa 'app="ATLASSIAN-JIRA"'
```

Outcome: each `-<engine>` flag takes that engine's own query language, so syntax-sensitive searches (FOFA `app=`, Censys field filters) run correctly in a single command instead of being forced through one generic form.

### Emit structured output for pipelines

Keep full host details, not just ip:port.

```
uncover -q jira -e shodan -j -o results.jsonl -f ip,port,host
```

Outcome: `-j/--json` writes JSONL records to results.jsonl, `-f/--field ip,port,host` controls which fields appear in non-JSON output, and the file feeds asset trackers or diffing between runs.

*No corpus evidence — derived from upstream documentation.*
