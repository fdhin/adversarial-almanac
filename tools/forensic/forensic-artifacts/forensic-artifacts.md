---
name: "forensic-artifacts"
categories:
  - forensics
version: "20230928-2"
homepage: "https://github.com/ForensicArtifacts/artifacts"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: python
meta-checked: "2026-11-09"
github-repo: "ForensicArtifacts/artifacts"
github-stars: 1285
github-pushed: "2026-11-01"
github-archived: false
github-issues: 38
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 35
gh-activity-checked: "2026-11-09"
---

# forensic-artifacts

> Community-maintained, machine-readable knowledge base of digital forensic artifact definitions (a YAML dataset) with a small Python library and validator tooling.

## What it's for

This is the artifact repository behind ForensicArtifacts.com: a collection of YAML files, each defining one forensic artifact — its name, documentation, supported operating systems, and `sources` describing where the data lives (file paths, registry keys, knowledge base entries, WMI queries and the like). It is a dataset first and a library second: any tool can consume the data by reading YAML, and the included Python package (`artifacts`) exists mainly to parse and validate the definitions. DFIR tools use it to know which locations to collect and parse on a target system; humans use it as a reference of what an artifact is and where to find it. It does not collect or parse anything itself — it is the knowledge base that collection and parsing tools are built against.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Search the dataset for an artifact of interest

```
grep -ril 'browserhistory' /usr/lib/python3/dist-packages/artifacts/data/
```

Outcome: lists the YAML files whose artifact definitions mention browser history (e.g. `browsers.yaml`), which can then be opened to see name, description and source paths for each browser's data locations. Adjust the path to wherever the package's `data/` directory is installed.

### Look up where an artifact lives on a system

```
sed -n '/name: SafariHistory$/,/^---/p' /usr/lib/python3/dist-packages/artifacts/data/webbrowser.yaml
```

Outcome: prints the full definition block of the named artifact — its `doc`, `sources` (paths/keys to collect) and `supported_os` — the exact locations a collector or parser should target.

### Validate definitions after editing or adding artifacts

```
validator path/to/artifacts/data
```

Outcome: checks every `*.yaml` under the given file or directory against the definition format and prints validation errors per file; exit status is non-zero when any definition is invalid, which is what the project's CI uses to keep the dataset well-formed.

### Generate statistics about the dataset

```
stats
```

Outcome: prints counts about the shipped definitions (number of artifacts, breakdown by source type and supported OS), useful to sanity-check a checkout or packaged copy of the dataset.

### Load the definitions from Python tooling

```
python3 -c "from artifacts import reader; r = reader.YamlArtifactsReader(); defs = list(r.ReadDirectory('/usr/lib/python3/dist-packages/artifacts/data')); print(len(defs))"
```

Outcome: parses the whole dataset via the library and prints the number of artifact definitions loaded — the same reader (`YamlArtifactsReader.ReadFile` for a single file) that consumer tools embed to turn the YAML into `ArtifactDefinition` objects.
