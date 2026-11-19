---
name: "gene"
categories:
  - blackarch-windows
version: "78.faf8cc0"
homepage: "https://github.com/0xrawsec/gene"
packaged-by: [blackarch]
blackarch-builddate: "2022-12-02"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0xrawsec/gene"
github-stars: 172
github-pushed: "2023-11-13"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gene

> Go detection engine that matches signature rules against Windows event logs (EVTX) and any JSON-formatted logs.

## What it's for

gene (Gene[sis]) was built for incident responders who repeatedly wrote
one-off scripts to match Windows EVTX entries against findings; it turns
that into shareable detection rules; it can now match against any JSON
event stream. It is a matcher, not a collector: you point it at EVTX files
or JSON log streams together with a rule directory, and it prints every
event that matches, as JSON, with optional MITRE ATT&CK annotation. Rules
follow the gene rule format (schema version 2.x); a community rule set
lives in the `0xrawsec/gene-rules` repository, and the tool pairs well
with telemetry producers such as Sysmon or Kunai. It does not forward,
store or aggregate logs — use `-reduce` to condense results after a scan.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scan an EVTX file against a rule set

Hunt through a collected Windows event log with detection rules.

```
gene -r rules/ security.evtx
```

Outcome: parses the EVTX natively, evaluates every event against the
loaded rules, and prints each matching event as a JSON object (exit with
no output when nothing matches).

### Match rules over a JSON event stream

Apply the same rules to JSON logs piped from another tool.

```
cat events.json | gene -j -r rules/ -
```

Outcome: reads JSON events from stdin (`-j` selects JSON input mode,
`-` means stdin) and prints the matching events to stdout, so it can sit
at the end of any log-processing pipeline.

### Verify a rule directory before deployment

Check that rules load and compile cleanly.

```
gene -r rules/ -verify
```

Outcome: loads every rule file, reports syntax or schema errors, and
exits without scanning any logs — a quick CI check when editing rules.

### Annotate matches with timestamps and ATT&CK context

Get analyst-friendly output during triage.

```
gene -t -a -r rules/ security.evtx
```

Outcome: matching events are printed with their Unix timestamp and any
MITRE ATT&CK technique information the rules carry, making the output
directly usable in a timeline.
