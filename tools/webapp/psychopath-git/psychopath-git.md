---
name: "psychopath-git"
categories:
  - webapp
version: "20180724.r251"
homepage: "https://github.com/ewilded/psychoPATH"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "ewilded/psychoPATH"
github-stars: 276
github-pushed: "2021-02-12"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# psychopath-git

> Burp Suite extension (psychoPATH) for blind detection of path-traversal-vulnerable file uploads and LFI, plus a Perl docroot payload generator.

## What it's for

Hunting file-upload and LFI bugs that ordinary scanners and wordlists miss: blind webroot discovery, uploads landing in unlinked directories, and traversal with weak (non-recursive) filters. Its main form is a Burp Suite extension (shipped as `Java/psychoPATH.jar`, loadable from the Burp BApp Store or Extender) that adds four Intruder payload generators — directory checker, traversal/file-upload generators with evasive mutations (e.g. `....//....//`), a single-byte generator for bad-character hunting — with global output-encoding control (none/URL/double-URL) and dynamic webroot-list generation modeled on sqlmap's `--os-shell` technique. It is driven from Burp's GUI rather than a CLI; the repo also carries detailed per-scenario usage in `usage_examples.pdf`. The bundled Perl helper generates the same candidate DOCUMENT_ROOT path lists from the command line for any Intruder-style workflow.

## Typical use cases

### Generate candidate document-root payload lists

Produce the blind-traversal wordlist the extension builds internally, from the CLI.

```
cd psychoPATH/perl && vi get_docroots.pl   # set @targets and $filename in the CONFIG section
perl get_docroots.pl
```

Outcome: prints every candidate DOCUMENT_ROOT full path for the configured target names (optionally with traversal prefixes, pure-traversal payloads and filter-evasion mutations enabled by the `$auto_append_*` config flags) — feed the list to Burp Intruder or any fuzzer as the payload set for blind file-upload exploitation.

### Extend Burp Intruder with evasive traversal payloads

Load the extension and use its generators interactively (GUI workflow, no shell command).

```
Burp Suite -> Extender -> Add -> Extension type: Java -> select Java/psychoPATH.jar
```

Outcome: four psychoPATH payload generators (plus the Byte generator) become available in Intruder's payload-source dropdown; configure the target name/variables and output encoding in the extension's UI, then run the attack to blindly probe upload endpoints for webroot write access or LFI with filter-evasion variants.

*Provenance: no corpus evidence — derived from upstream documentation (README + `perl/get_docroots.pl`, verified 2026-11).*
