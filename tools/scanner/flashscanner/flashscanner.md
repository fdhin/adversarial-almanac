---
name: "flashscanner"
categories:
  - blackarch-scanner
version: "11.6815b02"
homepage: "https://github.com/riusksk/FlashScanner"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "riusksk/FlashScanner"
github-stars: 54
github-pushed: "2016-06-26"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# flashscanner

> riusksk's Perl Flash security scanner: finds SWF files by Google dork, by URL or on disk, then decompiles and checks them against the known Flash exploit database for vulnerable versions and XSS-prone constructs.

*No corpus evidence — derived from upstream documentation.*

## What it's for

FlashScanner audits Adobe Flash content. It takes its targets three ways — harvested from Google search results (`-g` with a starting result page `-p`), a direct URL (`-u`), or a local file (`-f`) — then runs its checks: matching the movie against the Flash vulnerability knowledge base (the web.appsec.ws Flash exploit database the upstream README cites) and looking for simple Flash-XSS patterns. Internally it leans on the `flare` Flash decompiler, which is a Windows/DOS binary, so on Linux hosts the bundled tool runs under an emulation layer. It is a detection tool for a technology stack that is itself end-of-life; its value today is auditing legacy content that still ships Flash.

## Typical use cases

### Harvest and audit SWFs through a Google dork

```
flashscanner -g "site:target.example.com filetype:swf inurl:player" -p 1
```

Outcome: the tool runs the dork through Google starting at result page 1, fetches each discovered SWF, decompiles it and prints which files match known-vulnerable Flash versions or contain XSS-suspect code.

### Audit one SWF by URL

```
flashscanner -u http://target.example.com/player.swf
```

Outcome: downloads that specific movie and reports the same findings for it — the quick way to vet a single embedded player you already know about.

### Audit a local SWF file

```
flashscanner -f player.swf
```

Outcome: the checks run directly on the local copy with no download, useful for reviewing your own builds or files already pulled from an engagement; `-h` prints the option summary.
