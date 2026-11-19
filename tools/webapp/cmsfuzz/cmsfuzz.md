---
name: "cmsfuzz"
categories:
  - blackarch-webapp
version: "5.6be5a98"
homepage: "https://github.com/nahamsec/CMSFuzz"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nahamsec/CMSFuzz"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# cmsfuzz

> Interactive Ruby content fuzzer that probes a site for files belonging to a known platform.

## What it's for

CMSFuzz answers "which files from platform X exist on this server?" You give it a URL and pick a target from its menu — WordPress, ColdFusion, Drupal, Joomla, PHP-Nuke, Magento, SharePoint, generic PHP files, backup files, Apache or IIS defaults, or miscellaneous leftovers (SVN, robots) — and it requests that platform's known file list (built on SecLists' CMS wordlists), reporting what returns hits. It is content discovery scoped to a platform, useful for finding forgotten installs, backup archives and default files. The tool is a menu-driven Ruby script with no command-line flags; the upstream repository is gone, so the packaged copy is the reference.

## Typical use cases

### Enumerate WordPress files on a target

Launch the fuzzer and walk its prompts.

```
cmsfuzz
```

Outcome: starts the interactive session — at the "Please enter target URL" prompt type the host (e.g. `target.example.com`), then choose `1` for WordPress. The script requests the WordPress file list and prints a line per finding (`200 FOUND AT:`, `REDIRECTION AT:`, `FORBIDDEN AT:` with the URL), giving you the surviving files to triage. Options 2-12 repeat the same flow for the other platforms and file classes.

### Hunt backup and leftover files

Use the non-CMS menu entries after the platform scan.

```
cmsfuzz
```

Outcome: entering the same target and choosing `9` (backup files) or `12` (miscellaneous files such as SVN and robots) probes those lists instead — a quick sweep for archives and source-control residue that survives recon of the visible application.

No corpus evidence — derived from upstream documentation.
