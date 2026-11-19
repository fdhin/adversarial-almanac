---
name: "openrisk"
categories:
  - blackarch-misc
version: "v0.0.1.r22.gb536ea0"
homepage: "https://github.com/projectdiscovery/openrisk"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2023-05-21"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/openrisk"
github-stars: 179
github-pushed: "2026-11-05"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 52
gh-activity-checked: "2026-11-09"
---

# openrisk

> Turns Nuclei scan results into a single AI-generated 1-10 risk score for a host.

## What it's for

openrisk reads Nuclei scan output (`.txt`, `.md` or `.jsonl` — a single file or a directory) and asks OpenAI's GPT-4o model to summarize the findings as a 10-scale risk score for the scanned host. It is built for triage: collapsing a finished scan of one target into a one-line verdict a report or ticket can quote, rather than performing vulnerability analysis of its own. It requires an `OPENAI_API_KEY`, works against a single target at a time, and is explicitly experimental (a ProjectDiscovery research project) — treat the score as a summary of the Nuclei findings, not new detection.

## Typical use cases

### Score a finished Nuclei scan

Turn a saved scan result into a risk verdict without reading every finding.

```
export OPENAI_API_KEY=<OPENAI_API_KEY>
openrisk -f nuclei_results.txt
```

Outcome: prints a `[RISK SCORE]` line with a 10-scale score and a one-sentence rationale naming the most severe findings (for example exposed panels or takeover-able services).

### Score a directory of result files

Process a whole output folder in one run.

```
openrisk -f nuclei-output/
```

Outcome: reads every supported result file under the directory and emits the GPT-generated risk summary for the scan set.

*No corpus evidence — derived from upstream documentation.*
