---
name: "rita"
categories:
  - blackarch-recon
version: "v5.1.2.r5.g1317d70"
homepage: "https://github.com/activecm/rita"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-06-29"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "activecm/rita"
github-stars: 668
github-pushed: "2026-11-07"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 18
gh-activity-checked: "2026-11-09"
---

# rita

> Ingests Zeek logs into datasets and hunts for beacons, long connections, DNS tunnels and threat-intel hits across your network traffic.

## What it's for

RITA (Real Intelligence Threat Analytics, by Active Countermeasures) is an open-source network-traffic-analysis framework for threat hunting. It imports Zeek logs (TSV or JSON) into named datasets and scores them for beaconing behavior (in and out), abnormally long connections, DNS tunneling, and matches against threat-intel feeds. The v5 CLI is dataset-oriented: `import` builds a dataset, `view` explores it in a terminal UI or dumps filtered CSV, `list`/`delete` manage datasets, `zone-transfer` checks a configured domain, and `validate` checks config files. Note for anyone following older material: v4-era subcommands like `rita db` and `rita exfil-beacon` no longer exist in v5 — their analyses live inside the unified `view` results with the beacon score column. RITA itself does not capture traffic; it needs Zeek logs as input.

## Typical use cases

### Import Zeek logs into a dataset

```
rita import --database=mydataset --logs=~/zeek-logs
```

Outcome: parses the log directory into the named dataset (databases must start with a lowercase letter, alphanumeric/underscore only), running the beaconing, long-connection, DNS and threat-intel analytics; import again into the same dataset to accumulate data, or add `--rebuild` to wipe and re-import from scratch.

### Maintain a rolling window of recent traffic

```
rita import --database=weekly --logs=~/zeek-logs --rolling
```

Outcome: builds and trims the dataset so it always covers a fixed recent window — the intended mode for a cron job that imports each hour's logs from a Zeek sensor; leave `--rolling` off for backfills of logs older than 24 hours.

### Review a dataset interactively

```
rita view mydataset
```

Outcome: opens the terminal UI over the analyzed dataset, where connections are filtered with a GitHub-style query syntax — e.g. `src:192.168.88.2 dst:165.227.88.15 beacon:>=90 sort:duration-desc` — using fields like `src`, `dst`, `beacon`, `duration`, `subdomains`, `threat_intel` and `severity` to isolate the strongest beaconing candidates.

### Dump filtered results as CSV

```
rita view --stdout -s="beacon:>=90, severity:high" mydataset
```

Outcome: instead of the TUI, pipes comma-delimited rows to stdout, filtered by the search criteria (`-s` requires `--stdout`/`-o`) — ready for spreadsheets or downstream tooling; `rita list` shows every dataset on the host and `rita delete --non-interactive mydataset` reclaims one without a confirmation prompt.

*No corpus evidence — derived from upstream documentation (README and cmd/ source tree).*
