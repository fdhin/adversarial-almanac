---
name: "droopescan"
categories:
  - blackarch-webapp
version: "1.45.1"
homepage: "https://github.com/droope/droopescan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: AGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "droope/droopescan"
github-stars: 1453
github-pushed: "2024-01-19"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# droopescan

> A plugin-based scanner that aids security researchers in identifying issues with several CMSs, mainly Drupal & Silverstripe.

## What it's for

droopescan is a plugin-based CMS enumerator: it fingerprints the CMS version and enumerates installed plugins, themes and interesting URLs (admin panels, changelogs) by checksumming known files. Drupal and SilverStripe are fully supported, WordPress nearly so, with partial plugin/theme coverage for Joomla and Moodle. Reach for it during web enumeration when you need a Drupal or SilverStripe install's version and module inventory fast — it is threaded, and any correlation between the found versions and known CVEs stays your job (the tool deliberately stops at identification). It is not a general web scanner: no crawling, no exploitation, and `.netrc` basic auth plus `http_proxy`/`https_proxy` environment variables are the supported ways to reach authenticated or proxied targets.

## Typical use cases

### Enumerate a Drupal site

The canonical single-target run.

```
droopescan scan drupal -u http://target.example.com
```

Outcome: runs all four checks (plugins, themes, version, interesting URLs) and prints sections such as `[+] Possible version(s): 7.34`, `[+] Plugins found:` with each module's URL, and interesting URLs like the default changelog and `/user/login` — the module list plus version is what you feed into CVE matching.

### Enumerate a SilverStripe site

Same sweep for the other fully supported CMS.

```
droopescan scan silverstripe -u http://target.example.com
```

Outcome: enumerates SilverStripe modules, themes and possible versions using that CMS's checksum database, in the same sectioned report as the Drupal run.

### Identify the CMS first, then scan

Omit the CMS argument to auto-detect.

```
droopescan scan -u http://target.example.com
```

Outcome: sends identification requests first, then runs the full scan only if the site is a supported CMS — the right form when you do not already know what the target runs.

### Sweep a fleet of sites from a file

Scan many URLs in one command.

```
droopescan scan drupal -U list_of_urls.txt
```

Outcome: scans every URL in the file (one per line; a URL plus an overriding Host header separated by a tab also works) and prints one result block per site — the standard shape for covering an organisation's sites in a pass.

### Restrict the run to version checks

Only the checks you want, faster and quieter.

```
droopescan scan drupal -U list_of_urls.txt -e v
```

Outcome: `-e`/`--enumerate` selects checks (`v` version, `p` plugins, `t` themes, `i` interesting URLs); combined with a URL file this produces one compact line per site, e.g. `{"host": "...", "version": {"finds": ["7.6"]}}`.

### Get machine-readable output

Emit JSON for pipelines.

```
droopescan scan drupal -u http://target.example.com --output json
```

Outcome: replaces the human report with a stable JSON object (keys `themes`, `plugins`, `version`, `interesting urls`), one object per line in multi-site runs; `--output standard` is the default text form. Throughput knobs for big sweeps: `-t`/`--threads` (default 4) and `-n`/`--number` of concurrent requests.

Corpus-grounded and verified against the upstream droopescan README and source (scan controller flag definitions).
