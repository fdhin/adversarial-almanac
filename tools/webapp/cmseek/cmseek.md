---
name: "cmseek"
categories:
  - blackarch-webapp
version: "387.03a6e7f"
homepage: "https://github.com/Tuhinshubhra/CMSeeK"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-19"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Tuhinshubhra/CMSeeK"
github-stars: 2594
github-pushed: "2026-07-17"
github-archived: false
github-issues: 37
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# cmseek

> CMS detection suite with deep WordPress and Joomla enumeration scans.

## What it's for

CMSeeK identifies which content management system a site runs (180+ signatures via headers, generator tags, source, robots.txt and directory probes), then runs CMS-specific deep scans: WordPress version/user/plugin/theme enumeration with version-vulnerability lookups, Joomla version detection, backup-file and admin-page finding, config-leak and directory-listing checks. It also ships a modular brute-force system with pre-made modules. It is a reconnaissance and known-vuln-mapping tool — it matches versions against public issues rather than actively exploiting. Results land in a per-site `cms.json` under the `Result/` directory, which is what you feed into the next phase of testing.

## Typical use cases

### Detect the CMS and run the deep scan for it

Single-target scan with the full detection pipeline.

```
cmseek -u target.example.com --random-agent -v
```

Outcome: fingerprints the CMS, then automatically runs that CMS's deep scan (for WordPress: version, users, plugins, themes plus version-vulnerability checks). Progress prints verbosely and the structured findings are written to `Result/target.example.com/cms.json`.

### Scan many sites from a list

Unattended batch enumeration across targets.

```
cmseek -l targets.txt --batch --follow-redirect
```

Outcome: scans every site in `targets.txt` (one per line or comma separated), following redirects before testing, never pausing for a keypress between sites, and writing one `cms.json` per target under `Result/`.

### Constrain detection to suspected CMS IDs

Cut false positives by limiting which signatures are considered.

```
cmseek -u target.example.com --strict-cms wordpress,joomla
```

Outcome: checks the target only against the given CMS IDs instead of the full 180+ set — useful when generic signatures misidentify a custom application.

### Guided interactive session

Explore options and brute-force modules from the menu.

```
cmseek
```

Outcome: opens the interactive menu where you can start scans by answering prompts, run the built-in CMS brute-force modules, and rebuild the module cache after adding your own.

No corpus evidence — derived from upstream documentation.
