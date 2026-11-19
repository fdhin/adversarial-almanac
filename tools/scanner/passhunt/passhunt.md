---
name: "passhunt"
categories:
  - blackarch-scanner
version: "5.332f374"
homepage: "https://github.com/Dionach/PassHunt"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Dionach/PassHunt"
github-stars: 65
github-pushed: "2014-07-11"
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

# passhunt

> Searches drives and mail stores for documents containing passwords.

## What it's for

PassHunt (Dionach), sibling of PANhunt, sweeps directories for files whose content matches a password-indicating regular expression (default: `password`). It opens text-ish documents (.doc, .xls, .xml, .txt, .csv) plus scripts and configs (.ini, .config, .bat, .vbs, .php, .sh and more), recurses Office XML/zip containers, parses Outlook .msg/.pst mail including attachments, and lists — but does not search — binary formats like .ost, .accdb, .mdb, .kdb and certificates. Results go to a timestamped HTML report showing each file and its matching content. Built as a simple USB-stick audit tool for finding credentials left on workstations and shares; the packaged console is `passhunt`.

## Typical use cases

### Sweep a share for password-bearing documents

Find credentials forgotten in files on a mounted drive.

```
passhunt -s /mnt/share -o report.html
```

Outcome: walks the tree opening the configured document, script and config extensions plus mail stores, prints progress, and writes an HTML report listing every file with password matches (default report name `passhunt_<timestamp>.html`). Defaults assume Windows paths, so set `-s` and `-x` explicitly on other systems.

### Restrict the search to configuration file types

Cut runtime by scoping the extension lists.

```
passhunt -s /mnt/share -t .ini,.conf,.config -z .zip
```

Outcome: only the named text extensions and zip containers are opened — the quick pass for plaintext credentials in configuration files; `-m` adjusts the mail extensions (.pst default).

No corpus evidence — derived from upstream documentation (Dionach/PassHunt README usage block).
