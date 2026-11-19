---
name: "glpwnme"
categories:
  - blackarch-scanner
version: "168.7101cd7"
homepage: "https://github.com/Orange-Cyberdefense/glpwnme"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Orange-Cyberdefense/glpwnme"
github-stars: 184
github-pushed: "2026-10-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 149
gh-activity-checked: "2026-11-09"
---

# glpwnme

> Vulnerability checker and exploit runner for GLPI instances: version fingerprinting, a catalogue of CVE checks, and actual exploitation with cleanup.

## What it's for

glpwnme (Orange Cyberdefense) targets GLPI, the open-source ITSM/asset-management platform. Point
it at a running instance and it fingerprints the version and matches it against a catalogue of
known GLPI vulnerabilities — unauthenticated RCEs, SQL injection, default credentials, document
leaks, user enumeration, and plugin CVEs. It goes beyond checking: most exploits implement a
`--run` mode that actually exploits, an `--infos` mode that explains usage and options, and a
`--clean` mode that reverses the traces (recorded in `log.glpwnme`). Checks are opsec-safe by
default, with `--no-opsec` unlocking the intrusive ones; authentication via credentials or cookie
is supported for exploits that need a session, and `--find-by-version` maps a version number to
its vulns without touching the target.

## Typical use cases

### Enumerate what applies to a target

See which exploits the detected GLPI version admits.

```
glpwnme -t https://target.example.com
```

Outcome: fingerprints the instance and prints the compatible exploits from the catalogue with
their privilege requirements — the triage pass before running anything.

### Run the full check suite

Test everything the version allows in one pass.

```
glpwnme -t https://target.example.com --check-all --no-opsec
```

Outcome: executes every applicable exploit check; dropping `--no-opsec` restricts the run to the
opsec-safe subset. Findings print inline as each check completes.

### Exploit a specific CVE

Go from check to actual exploitation on a chosen vulnerability.

```
glpwnme -t https://target.example.com -e CVE_2022_35914 --run
```

Outcome: runs the named exploit (precede it with `--infos` to see its options, e.g.
`-O "command=id"`, and auth flags like `-u`/`-p` or `--cookie` where a session is required).
Afterwards inspect `log.glpwnme` for what was changed — it records artifacts such as moved
`.htaccess` files and recovered API keys.

### Clean up after exploitation

Remove the traces an exploit left behind.

```
glpwnme -t https://target.example.com -e CVE_2022_35914 --clean
```

Outcome: calls the exploit's clean method to restore what `--run` modified, using the same
options originally passed.

### Audit installed plugins

Check plugin versions the core version scan does not cover.

```
glpwnme -t https://target.example.com --list-plugins
```

Outcome: enumerates the target's GLPI plugins and reports vulnerable ones (e.g. the Order plugin
below 2.10.1) — plugin CVEs that `--check-all` alone can miss.

*No corpus evidence — derived from upstream documentation (Orange-Cyberdefense/glpwnme README).*
