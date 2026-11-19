---
name: "apkleaks"
categories:
  - blackarch-mobile
version: "v2.6.3.r2.gdd5271a"
homepage: "https://github.com/dwisiswant0/apkleaks"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "dwisiswant0/apkleaks"
github-stars: 6381
github-pushed: "2025-08-20"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# apkleaks

> Scans an APK's decompiled sources for URIs, endpoints and hardcoded secrets.

## What it's for

APKLeaks decompiles an APK with jadx (prompting to download it on first run if not found) and runs a curated regex pattern set over the recovered sources — built-in patterns from `config/regexes.json` cover cloud keys, API tokens and similar credential shapes, plus LinkFinder-derived URL/endpoint extraction. Findings are printed and written to a results file whose path is chosen randomly unless you set `-o`. It is a grep over decompiled code: every hit is a lead to verify manually, not a confirmed live credential.

## Typical use cases

### Scan an APK for URIs and secrets

One-command triage of what an app embeds.

```
apkleaks -f target.apk
```

Outcome: decompiles `target.apk` with jadx, prints matches grouped by pattern name (URLs/endpoints plus any secret-shaped strings), and writes the full results to an auto-named file in the working directory, with the path printed at the end.

### Save JSON results to a known path

Make output deterministic and pipeline-friendly.

```
apkleaks -f target.apk -o results.json --json
```

Outcome: writes findings to `results.json` in JSON format instead of an auto-named text file; omit `--json` to get the text format at the same path.

### Scan with custom patterns

Add product-specific token formats to the hunt.

```
apkleaks -f target.apk -p custom-rules.json -o results.txt
```

Outcome: replaces the built-in regex set with your own JSON mapping of name to pattern (e.g. `{"Example API key": "EXA[0-9A-Z]{16}"}`); matching findings land in `results.txt` under those names.

### Pass options to the disassembler

Tune the jadx decompilation step for big or obfuscated apps.

```
apkleaks -f target.apk --args "--deobf --threads-count 5"
```

Outcome: forwards the quoted flags to the underlying jadx invocation via `-a`/`--args` — e.g. enabling deobfuscation or thread control — before the regex scan runs.

*No corpus evidence — derived from upstream documentation.*
