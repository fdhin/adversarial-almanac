---
name: "fimap"
categories:
  - blackarch-webapp
version: "230.41eca15"
homepage: "https://gitlab.tha-imax.de/root/fimap"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-23"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "crunchsec/fimap"
github-stars: 10
github-pushed: "2015-07-03"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fimap

> Finds, audits and exploits local/remote file inclusion (LFI/RFI) bugs — in intent, "sqlmap for LFI/RFI".

## What it's for

fimap (by Iman Karim) audits web applications for file inclusion vulnerabilities: it tests GET parameters, POST data and cookies for `include`/`require` bugs, then escalates confirmed findings — reading remote files, spawning shells and reverse shells from its interactive exploit mode, with null-byte and dot-truncation techniques to defeat appended suffixes. Blind mode (`--enable-blind`) finds bugs on servers with error display off, and a plugin interface extends the exploit mode. This is the classic Python 2 release, packaged from the author's own GitLab repository; a separate Python 3 rewrite by a different maintainer exists at github.com/kurobeats/fimap with a changed interface.

GitHub: third-party mirror of the author's GitLab repository (gitlab.tha-imax.de/root/fimap).

No corpus evidence — derived from the packaged upstream source.

## Typical use cases

### Audit a single URL

Check one suspect parameter.

```
fimap -u 'http://target.example.com/page.php?include=VULN'
```

Outcome: single-URL mode (the default) probes the parameter for file-inclusion errors; confirmed bugs are recorded into `fimap_results.xml` for later exploitation.

### Mass-audit a list of URLs

Work through harvested or recon-generated lists.

```
fimap -m -l urls.txt
```

Outcome: mass mode (`-m`/`--mass`) checks every URL in the list for inclusion bugs and accumulates findings in the shared results file.

### Test targets that suppress errors

Find bugs without error messages.

```
fimap -u 'http://target.example.com/page.php?include=VULN' -b
```

Outcome: blind testing (`-b`/`--enable-blind`) issues more requests but detects file inclusion on servers where error output is disabled; automatic language detection can be overridden with `--no-auto-detect`.

### Harvest URLs for later auditing

Collect a target's URLs first, audit them second.

```
fimap -H -u http://target.example.com/ -w harvested.txt -d 2
```

Outcome: harvest mode (`-H`) crawls the site recursively to depth 2 (`-d`, default 1) and appends every discovered URL to `harvested.txt`, ready to feed back through mass mode.

### Exploit found bugs interactively

Escalate recorded findings.

```
fimap -x
```

Outcome: opens the interactive exploit session listing the vulnerable scripts found so far; selecting one offers file read, shell and reverse-shell actions (`-X` also lists findings that need plugins, and `--x-cmd` runs commands non-interactively).
