---
tool: "gtfobins"
build-system: "scripts"
upstream: "https://github.com/GTFOBins/gtfobins.github.io"
recipe: "upstream"
license: "GPL-3.0"
---

# Installing gtfobins

> Curated dataset of Unix executables that bypass local security restrictions (sudo/SUID abuse), consumed as offline data.

Source: pentest-book feed; upstream [`GTFOBins/gtfobins.github.io`](https://github.com/GTFOBins/gtfobins.github.io), rendered at [gtfobins.org](https://gtfobins.org).

## What installing means for a dataset

GTFOBins is not a program — there is nothing to build or run. "Installing" it means keeping a clone of the data so lookups work offline, then querying the files with `grep`/`yq` (see the tool note for the query patterns) or pointing a consumer tool at it.

## Get the data (any distro)

```bash
sudo git clone https://github.com/GTFOBins/gtfobins.github.io.git /opt/gtfobins
```

Update later with `git -C /opt/gtfobins pull`. Any directory works — `/opt/gtfobins` is the path used in the note's examples.

## Layout (what you cloned)

- `_gtfobins/<name>` — one YAML file per executable (~480 entries); extension-less files, with a few thin `alias:` pointers to canonical entries
- `_data/functions.yml` — the abuse-function taxonomy (shell, file-read, sudo, ...) with MITRE ATT&CK mappings
- `_data/contexts.yml` — privilege contexts (`unprivileged`, `sudo`, `suid`, `capabilities`)

## Notes

- No `/usr/bin` entry and no distro package — the reference copy is your clone (or the website, gtfobins.org).
- Rebuilding the *website* needs Jekyll (`Gemfile` in the repo) — irrelevant for data lookups; skip it.
- Vault consumers that query this dataset: [[gtfoblookup]], [[gtfo]].

Backlinks: [[wordlist]] · [[gtfobins]]
