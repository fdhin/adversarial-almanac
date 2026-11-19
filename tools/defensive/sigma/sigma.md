---
name: "sigma"
categories:
  - blackarch-defensive
version: "0.20"
homepage: "https://github.com/SigmaHQ/sigma/releases"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-15"
build-system: python
license: LGPL3
meta-checked: "2026-11-09"
github-repo: "SigmaHQ/sigma"
github-stars: 11424
github-pushed: "2026-11-07"
github-archived: false
github-issues: 325
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 310
gh-activity-checked: "2026-11-09"
---

# sigma

> Open YAML signature format for SIEM detections, plus the community rule repository and legacy sigmac converter.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Sigma is a generic, open signature format for describing detections on log events — "Sigma is for log files what Snort is for network traffic and YARA is for files." A rule is a YAML file that names a log source, expresses detection logic over fields, and carries metadata (level, status, MITRE references); because the format is product-neutral, one rule converts into queries for many SIEM platforms. The SigmaHQ/sigma repository is the main community rule collection — thousands of generic, threat-hunting, emerging-threat and compliance rules — and the release packaged here (0.20) also ships the legacy Python toolchain from its `tools/` directory: the `sigmac` rule converter plus helpers like `merge_sigma` and `sigma2misp`. Current-generation conversion is the pySigma-based CLI covered in the [[sigma-cli]] note; `sigmac` is the older interface still packaged under this name.

## Typical use cases

### List conversion targets and field mappings

See what query languages the converter supports before writing a pipeline.

```
sigmac --lists
```

Outcome: prints every supported output target (splunk, es-qs, graylog, and many more) together with the available field-mapping configurations; `sigmac --backend-help <target>` documents a specific target's options.

### Convert rules to Splunk queries

Turn platform-neutral detections into SPL searches.

```
sigmac -t splunk -c tools/config/generic/sysmon.yml -r rules/windows/process_creation
```

Outcome: emits one SPL query per rule to stdout, with the `-c` config mapping Sigma field names to Sysmon field names for the target environment.

### Convert a rule directory, ignoring per-backend failures

Bulk-translate a ruleset and keep going when some rules don't fit the backend.

```
sigmac -I -t splunk -c splunk-windows -r rules/windows/sysmon/
```

Outcome: converts every rule it can; `-I` skips rules that fail backend conversion instead of aborting the run, so you still get the bulk of the queries in one pass.

### Filter by rule metadata

Only deploy detections at the severity your SOC actually works.

```
sigmac -t es-qs -c tools/config/generic/sysmon.yml -f 'level>=high' -r rules/windows/sysmon/
```

Outcome: converts only rules whose `level` field satisfies the filter, emitting Elasticsearch query-string queries for high and critical detections.

### Write converted queries to files

Persist conversion output instead of piping stdout.

```
sigmac -t es-qs -c tools/config/generic/sysmon.yml -o es-queries/ -r rules/windows/process_creation
```

Outcome: because the `-o` value ends in `/`, each rule's query is written as its own file under `es-queries/` rather than everything going to one stream or a single file.
