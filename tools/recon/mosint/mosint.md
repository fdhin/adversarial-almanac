---
name: "mosint"
categories:
  - recon
version: "git (2024-02-02)"
homepage: "https://github.com/alpkeskin/mosint"
origin: "kitploit"
recipe: "upstream"
packaged-by: [aur]
build-system: go
meta-checked: "2026-11-09"
github-repo: "alpkeskin/mosint"
github-stars: 6074
github-pushed: "2024-02-02"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 28
---

# mosint

> Automated email OSINT: breaches, leaked passwords, related addresses, in one run.

## What it's for

Mosint (by Alp Keskin) takes a single email address and fans out across free and keyed services — syntax/validity checking, related emails and domains (hunter.io), breach-site reputation (emailrep.io), password-leak databases (Intelligence X, BreachDirectory, HaveIBeenPwned) and pastebin dumps (psbdmp.ws) — then prints a consolidated report. It is a querying tool, not an attacker: it reads what those services already know and never touches the mailbox itself. A config file is mandatory before the first run — without API keys in `$HOME/.mosint.yaml` it exits immediately — and results are only as deep as the keys you supply (unkeyed checks like DNS/IP lookup and pastebin search work without any). No corpus evidence — derived from upstream documentation.

## Typical use cases

### Profile an email address

Run the full check against one target.

```
mosint user@example.com
```

Outcome: validates the address syntax, then prints a color-coded report grouping what each service returned: related emails, breach-site names, leak hits and pastebin references. Exits with a config-file error until `~/.mosint.yaml` exists.

### Use an alternate config file

Point at a specific key set.

```
mosint -c api-keys.yaml user@example.com
```

Outcome: `-c`/`--config` overrides the default `$HOME/.mosint.yaml` path — handy for separate personal/client key sets; the file format is shown in the repo's `example-config.yaml` (service keys under `services:`, tuning under `settings:`).

### Silent JSON report for automation

Skip the terminal report and keep the data.

```
mosint -s -o report.json user@example.com
```

Outcome: `-s` suppresses the interactive report and `-o` writes the full result set as JSON to `report.json`, making the run scriptable; exit code is non-zero on invalid syntax or missing config.
