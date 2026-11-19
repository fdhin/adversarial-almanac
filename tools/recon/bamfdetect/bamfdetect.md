---
name: "bamfdetect"
categories:
  - recon
version: "1.6.12"
homepage: "https://github.com/bwall/bamfdetect"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "bwall/bamfdetect"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# bamfdetect

> Identify bot/malware families in samples with YARA and extract their configuration as JSON.

## What it's for

BAMF Detect (Brian Wallace, @botnet_hunter — the project grew out of his earlier `bamf` repo) reads files into RAM, unpacks UPX-compressed PE files when needed, matches them against per-family YARA signatures, and for a match extracts the malware's embedded configuration (C2 addresses, keys, campaign IDs) into readable JSON. It ships modules for a large roster of families — Andromeda, DarkComet, XtremeRAT, njRat, Pony, PoisonIvy, BlackShades, NanoCore, Bozok, CyberGate and many more. Aimed at botnet researchers and incident responders triaging bot samples, it is a classifier plus config extractor, not a sandbox or a generic AV scanner. The upstream repository is gone; this documents the final v1.6.x CLI.

## Typical use cases

### Scan a sample directory recursively

```
bamfdetect -r /opt/samples
```

Outcome: every file under the directory is matched against all family modules; for each recognized sample, JSON is printed with the family and its extracted configuration (e.g. C2 URLs and ports), ready to redirect into a report or feed.

### Detect families without extracting configs

```
bamfdetect -d sample.exe
```

Outcome: reports only which malware family each file matches — a fast triage pass when the configuration blobs are not needed.

### Restrict the scan to specific families

```
bamfdetect -m darkcomet -t 4 /opt/samples
```

Outcome: only the named module's YARA rules are applied, with 4 worker threads; useful when checking a batch against one suspected family.

### List the supported family modules

```
bamfdetect -l
```

Outcome: prints every malware-detection module bundled with the installed version, so you know which families a scan will cover before running it.

*No corpus evidence — derived from upstream documentation (archived project snapshot; upstream repository deleted).*
