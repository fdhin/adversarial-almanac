---
name: "viper"
categories:
  - blackarch-disassembler
version: "2099.fdd7855"
homepage: "https://github.com/viper-framework/viper"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-14"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "viper-framework/viper"
github-stars: 1558
github-pushed: "2023-06-07"
github-archived: true
github-issues: 58
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# viper

> Binary analysis and sample-management framework: a console (plus optional web interface) for organizing malware and exploit collections in projects, running analysis modules, and keeping notes, tags and results attached to each file.

## What it's for

Viper gives analysts a local sample repository: files are stored by hash, organized into projects, and every session can run modules (PE/ELF parsing, YARA, ssdeep fuzzy hashing and more, fetched from the community modules repository) whose results are stored alongside the sample together with tags, notes and parent/child relationships. The packaged version is the Viper 2.0 development line of the former botherder/viper repository, now living under the viper-framework organization — upstream is archived, this line is discontinued, and a further refactor lives in viper-framework/viper2 — so treat it as stable-but-frozen. It is file-centric triage and organization, not an interactive disassembler. Not to be confused with [[vipermonkey]], the unrelated VBA-emulation tool.

## Typical use cases

### Open a sample and store it in the repository

Start a session on a file and file it away.

```
viper -f /tmp/sample.exe
store
```

Outcome: viper launches its console with a session already open on /tmp/sample.exe; `store` copies the file into the local repository (keyed by SHA-256 under the storage path) after which it can be reopened by hash. Use `store --delete` to also remove the original copy after storing.

### Bulk-import a directory of samples

Import a folder, filtering by type, size and name.

```
viper
store --folder /tmp/malware --file-type PE32 --file-size 10000000 --file-name apt_* --tags apt,trojan
```

Outcome: from the console, walks /tmp/malware and stores only files matching every filter — PE32 magic, at most 10 MB, names matching `apt_*` — tagged `apt,trojan`, building a queryable collection from a bulk sample drop.

### Find stored samples

Query the repository.

```
find tag rat
find all
find name mimikatz*
```

Outcome: lists matching files as a numbered table (name, mime, MD5). `find` supports the keys all, latest, name, type, mime, md5, sha1, sha256, tag, note, any and ssdeep. Reopen a listed result without retyping paths with `open --last 1`, or open a stored sample directly by its hash: `open 22f77c113cc6d43d8c12ed3c9fb39825`.

### Export the current session

Hand a sample to another tool or system.

```
export -z sample.zip
```

Outcome: writes the session's file to the zip archive sample.zip. Use `-7 archive.7z` for 7z instead — the only format where the `-p`/`--password` option can protect the archive with a password.

### Review stored module results

Re-read earlier analysis without re-running modules.

```
analysis -l
analysis -v 6
```

Outcome: `analysis -l` lists the module commands already run against the open file (ID, command line, timestamp); `analysis -v 6` re-prints the stored output of result #6 — much faster than re-executing a slow scan. Stored results nobody needs anymore can be dropped with `analysis -d`.

### Work in a dedicated project

Keep one campaign out of the default project.

```
viper -p apt-campaign
```

Outcome: opens (creating if needed) the `apt-campaign` project, so all stores, notes and tags go to that project's own database under the storage path instead of the default; switch between existing projects later with the `projects` console command.

*No corpus evidence — derived from upstream documentation.*
