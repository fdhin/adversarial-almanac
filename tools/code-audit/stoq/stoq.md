---
name: "stoq"
categories:
  - blackarch-code-audit
version: "769.8bfc78b"
homepage: "https://github.com/PUNCH-Cyber/stoq"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "PUNCH-Cyber/stoq"
github-stars: 391
github-pushed: "2022-06-27"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stoq

> Plugin-driven automation framework for file analysis: payloads flow from providers (single files, stdin, monitored directories, pubsub) through worker plugins that scan and decode, with archivers storing raw payloads and connectors shipping results onward.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Automating the repetitive parts of file triage — extracting attachments, running the same extractors and scanners over every artifact, saving everything in a consistent structure. stoQ itself is the dispatcher and data model: the actual analysis lives in plugins (workers, providers, connectors, archivers, decorators, dispatchers) that you install separately, and the framework wires them into a pipeline with async I/O. Reach for it when you need the same analysis flow applied to a continuous stream of payloads rather than one-off manual scanning. It is not a scanner with built-in detections — with no plugins installed it moves data but finds nothing. The CLI expects a stoQ home directory (`~/.stoq`, or `$STOQ_HOME`) holding the config and installed plugins.

## Typical use cases

### Scan a single file with installed plugins

```
stoq scan mybadfile.exe
```

Outcome: the file (or stdin, when piped) is dispatched through the installed worker plugins according to their dispatch rules, and the scan response — payload metadata plus each plugin's results — is printed to stdout.

### Force a file through specific workers

```
stoq scan mybadfile.exe -s yara
```

Outcome: the yara worker is added to the start of this payload's dispatch regardless of the normal dispatch rules, so the response is guaranteed to include its findings; add more worker names to `-s` to chain several.

### Continuously ingest a monitored directory and archive payloads

```
stoq run -P dirmon -A mongodb
```

Outcome: stoQ stays resident, the dirmon provider picks up each newly created file it sees, payloads are dispatched to workers, and the mongodb dest-archiver stores both raw payloads and results for later pivoting.

### Ingest from pubsub through fixed workers, save results to file

```
stoq run -a yara trid exif -P pubsub -C file
```

Outcome: every payload arriving on the pubsub provider is forced through the yara, trid and exif workers (`-a` always-dispatches them), and results are written out through the file connector.

### Install a plugin

```
stoq install path/to/plugin_directory
```

Outcome: the plugin is copied into stoQ's plugin directory (`--install_dir` overrides the destination, `--github` installs directly from a repository URL) and a success message names the install location.

### List available plugins

```
stoq list
```

Outcome: prints every plugin found in the plugin directories with its name, version, description and plugin classes — use it to confirm which workers, connectors and providers you can name in `scan`/`run` commands.
