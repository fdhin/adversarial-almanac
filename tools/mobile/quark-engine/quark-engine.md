---
name: "quark-engine"
categories:
  - blackarch-mobile
version: "21.6.2"
homepage: "https://github.com/quark-engine/quark-engine"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL
meta-checked: "2026-11-09"
github-repo: "quark-engine/quark-engine"
github-stars: 1736
github-pushed: "2026-11-07"
github-archived: false
github-issues: 80
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 129
gh-activity-checked: "2026-11-09"
---

# quark-engine

> Obfuscation-neglecting Android malware analysis: scores APKs against a community rule database.

## What it's for

Quark-Engine dissects an APK into the API calls and string constants each
method uses, then matches those behavior combinations against a rule database
(of the kind `freshquark` downloads) and scores every matched rule by
confidence and weight. Because the rules describe behaviors — permission
requests followed by sensitive API use, string decryption followed by C2
URLs — the scoring holds up on obfuscated samples, which is the point of the
"obfuscation-neglect" design. It ships the `quark` CLI, the `freshquark` rule
updater, a rule generator, and a Python scripting API for custom analysis.
It is an assessment and family-attribution aid, not an antivirus and not a
decompiler replacement.

## Typical use cases

### Update the rule database

Fetch the latest community rules before analyzing.

```
freshquark
```

Outcome: downloads the current rule set into Quark's local rules directory
(the default `-r`/`--rule` path for the `quark` command); run it whenever
rules are stale, since new rules widen what scans can match.

### Run a summary malware assessment

Get a one-screen verdict for a suspicious APK.

```
quark -a sample.apk -s
```

Outcome: prints the summary report — each matched rule with its confidence
level and weight, plus the aggregate score; `-s` accepts an optional rule or
label name to restrict the summary to that subset.

### Produce a detail report for documentation

Capture full per-rule evidence for a report.

```
quark -a sample.apk -d -o report.json
```

Outcome: `-d` prints the detail report (every stage of each matched behavior
with the instructions that satisfied it) and `-o` writes the whole result as
a JSON file; `-w out.html` instead generates a standalone web report.

### Draw the call graph of matched behaviors

Visualize how the flagged methods connect.

```
quark -a sample.apk -g png
```

Outcome: writes call-graph images of the rule-matching method chains into the
call graph output directory (`-g json` emits the graph data instead of
images).

### Filter by confidence and list extracted facts

Cut noise and inventory the sample quickly.

```
quark -a sample.apk -t 80 -p -i all
```

Outcome: `-t` restricts scoring output to rules at or above the chosen
confidence (100/80/60/40/20), `-p` lists the Android permissions the APK
requests, and `-i all` lists classes, methods and descriptors (`native` or
`custom` narrow the listing).

*No corpus evidence — derived from upstream documentation.*
