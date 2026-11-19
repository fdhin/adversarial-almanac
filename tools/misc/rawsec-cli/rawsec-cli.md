---
name: "rawsec-cli"
categories:
  - blackarch-misc
version: "1.2.0.r7.gf7a08c6"
homepage: "https://github.com/mBouamama/rawsec_cli"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "mBouamama/rawsec_cli"
github-stars: 29
github-pushed: "2026-11-01"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rawsec-cli

> Searches Rawsec's Cybersecurity Inventory catalog from the command line.

## What it's for

Queries Rawsec's Cybersecurity Inventory (inventory.raw.pm) — a curated catalog of security tools, resources, CTF platforms, and security-oriented operating systems — from the terminal. It full-text searches projects by name or description, lists everything in a category, and filters results by language, price (free or paid), whether the project is online, and availability on BlackArch; output can be a table, plain list, JSON, or CSV. Useful for discovering tooling for a task, comparing options in a niche, or checking whether a tool you just learned about is catalogued and maintained.

## Typical use cases

### Search the inventory for a keyword

Find every catalogued project whose name or description mentions a term.

```
rawsec-cli search jwt
```

Outcome: a table of matching projects is printed; when exactly one result matches, it is shown in the console and a browser tab opens on the project's website or source repository.

### List all tools in a category

Enumerate a whole tool category instead of searching.

```
rawsec-cli list tools binary_exploitation
```

Outcome: every tool catalogued under `binary_exploitation` is listed (other categories include `osint`, `cracking`, `web_application_exploitation`, `wireless`; `rawsec-cli list` prints them all, alongside `resources`, `ctf`, and `os`).

### Filter and export search results

Narrow a search and save it as structured data.

```
rawsec-cli search jwt --lang python --free --output json --output-file jwt-tools.json
```

Outcome: only free tools written in Python are matched and written as JSON to `jwt-tools.json`; `--output` also accepts `list`, `csv`, and `table`, and `--blackarch` restricts results to packages available on BlackArch.

### Browse security-oriented operating systems

Filter the OS catalog by its base system.

```
rawsec-cli list os --base Linux
```

Outcome: all inventory operating systems built on Linux are listed; the sub-category argument (`maintained`, `no_more_maintained`, `project_transferred`) narrows by project status.

*No corpus evidence — derived from upstream documentation.*
