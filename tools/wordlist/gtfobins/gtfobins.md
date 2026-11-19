---
name: "gtfobins"
categories:
  - wordlist
homepage: "https://gtfobins.org"
origin: "pentest-book"
recipe: "upstream"
packaged-by: []
build-system: scripts
license: GPL-3.0
github-repo: "GTFOBins/gtfobins.github.io"
github-stars: 13857
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
---

# gtfobins

> Curated reference dataset of Unix binaries that break out of restricted environments — every entry is machine-readable YAML documenting exactly how a given executable escalates under sudo, SUID, or file capabilities.

## What it's for

GTFOBins (the site is gtfobins.org, the repository GTFOBins/gtfobins.github.io) is a curated list of Unix-like executables that can be used to bypass local security restrictions in misconfigured systems. It is a website-as-dataset: what looks like a browsable wiki is generated from plain data files, and the reference workflow is cloning the data and querying it offline while you work a host. Per-binary entries live in `_gtfobins/<name>` (one YAML file per executable, ~480 of them); the abuse taxonomy lives in `_data/functions.yml` (`shell`, `command`, `reverse-shell`, `bind-shell`, `file-read`, `file-write`, `upload`, `download`, `library-load`, `privilege-escalation`, `inherit`, each mapped to MITRE ATT&CK techniques), and `_data/contexts.yml` defines the privilege contexts an entry can carry (`unprivileged`, `sudo`, `suid`, `capabilities`). Layout note: older revisions stored entries as Jekyll-style `_gtfobins/<name>.md` files; current master uses extension-less YAML files, plus ~20 thin `alias: <other>` pointer files for name variants (e.g. `_gtfobins/awk` just points at `mawk`) — resolve those to their target before grepping. This package ships no program and no CLI of its own — every use case below is a lookup against its data files, run against a local clone (here `/opt/gtfobins`, created in the install notes). CLI consumers already in the vault: [[gtfoblookup]] (offline search across GTFOBins/LOLBAS/WADComs/HijackLibs) and [[gtfo]] (terminal search of GTFOBins and LOLBAS).

## Typical use cases

### Look up how one binary escapes

Read the whole entry for an executable you found on the target host.

```
cat /opt/gtfobins/_gtfobins/find
```

Outcome: prints the YAML entry for `find` — its abuse functions (`file-read`, `file-write`, `shell`, ...) with the exact command lines, per-context overrides, and maintainer comments; the `code:` lines are copy-pasteable escalation commands to test on the host. If the file contains only `alias: <name>`, read the file it names instead.

### Extract the SUID-specific payload for a binary

Pull just the escape that matches the privilege context you actually have.

```
yq '.functions.shell[0].contexts.suid.code' /opt/gtfobins/_gtfobins/find
```

Outcome: prints only the shell escape for the SUID context (for `find`: `find . -exec /bin/sh -p \; -quit`) — the `-p` flag keeps root privileges, which is exactly the difference a raw shell command would miss; swap the path segment `.contexts.sudo` or `.functions.file-read[0]` to target another context/function.

### Enumerate binaries that can spawn a shell

Build a candidate shortlist from the dataset before touching the target.

```
grep -rlE '^  shell:' /opt/gtfobins/_gtfobins/ | sed 's|.*/||' | sort | head -40
```

Outcome: lists every binary with a documented `shell` function (the two-space anchor matches the function key, not `reverse-shell` or prose) — cross it against `ls` of world-writable, SUID, or sudo-allowed executables on the host to spot a working escalation; refine per-binary with the yq lookup above.

### List the abuse taxonomy

See the vocabulary of functions and contexts the data is organized by.

```
grep -E '^[a-z-]+:' /opt/gtfobins/_data/functions.yml
```

Outcome: prints the top-level function keys (`shell`, `command`, `reverse-shell`, `bind-shell`, `file-read`, `file-write`, `upload`, `download`, `library-load`, `privilege-escalation`, `inherit`) — the attribute names you can target in yq paths and greps; the same file's `label`/`description`/`mitre` fields document each one.

No corpus evidence — derived from upstream documentation.
