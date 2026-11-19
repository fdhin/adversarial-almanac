---
name: "dftimewolf"
categories:
  - blackarch-forensic
version: "894.ba28d46"
homepage: "https://github.com/log2timeline/dftimewolf"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "log2timeline/dftimewolf"
github-stars: 361
github-pushed: "2026-11-06"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 89
gh-activity-checked: "2026-11-09"
---

# dftimewolf

> Recipe-driven DFIR orchestrator from the log2timeline project that chains collectors, processors, and exporters into a single command.

## What it's for

dfTimewolf automates multi-step forensic workflows. A recipe names a chain of modules — collectors (local paths, GRR-monitored hosts, AWS/GCP/Azure resources, cloud audit logs), processors (plaso/log2timeline), and exporters (Timesketch) — that pass data on to one another. Instead of scripting collection, processing, and upload by hand, one `dftimewolf <recipe>` invocation runs the whole pipeline, with per-recipe arguments and reusable defaults configured in `~/.dftimewolfrc`. The heavy external tools (plaso, Timesketch and GRR clients) are not bundled and must be installed separately for the recipes that use them.

## Typical use cases

### Discover available recipes

```
dftimewolf -h
```

Outcome: lists every recipe shipped with the framework (aws_forensics, gcp_forensics, grr_artifact_ts, plaso_ts, upload_ts, vt_pcap, and the rest) with a one-line description; `dftimewolf <recipe> -h` then prints that recipe's positional and optional arguments.

### Process local evidence with plaso and push it to Timesketch

```
dftimewolf plaso_ts /cases/disk1.img,/cases/disk2.img --incident_id 12345
```

Outcome: runs log2timeline/plaso over the comma-separated paths and uploads the resulting timelines to a Timesketch sketch described by the incident ID — collection, processing, and export in one step. `--sketch_id` targets an existing sketch instead of creating one.

### Inspect and reuse workflow defaults

```
dftimewolf plaso_ts -h
```

Outcome: prints the recipe's full argument set (paths, `--incident_id`, `--sketch_id`, `--token_password`) plus the module chain description, so a run can be scripted or its parameters pinned in `~/.dftimewolfrc` as a JSON override.

*No corpus evidence — derived from upstream documentation.*
