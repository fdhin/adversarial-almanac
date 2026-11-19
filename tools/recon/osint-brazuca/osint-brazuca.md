---
name: "osint-brazuca"
categories:
  - recon
version: "git (2026-10-26)"
homepage: "https://github.com/osintbrazuca/osint-brazuca"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "osintbrazuca/osint-brazuca"
github-stars: 2845
github-pushed: "2026-11-08"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 163
gh-activity-checked: "2026-11-09"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# osint-brazuca

> Curated, machine-readable directory of Brazilian OSINT sources — 500+ lookup portals across 39 categories, shipped as JSON datasets alongside a Portuguese handbook.

## What it's for

osint-brazuca is not a scanner; it is a maintained reference repository of OSINT sources and techniques "dentro do contexto Brasil" — Brazilian federal and state portals for social benefits, missing and wanted persons, court records (PJe, e-SAJ, CNJ), company registries, leaks and more. The README is the human handbook (in Portuguese, with legal/LGPD warnings up front), and the same content is exported into a structured dataset under `data/`: `sources.json` is the canonical catalog (510+ sources in 39 categories, each with an id, accepted input types like `cpf` or `cnpj`, output types, links and source level), `index.json` flattens every URL into one record per link (1,200+), `taxonomy.json` defines the controlled vocabulary, and `overrides.json` holds manual classification fixes. Reach for it when an investigation touches Brazilian entities and you need the right official (or unofficial) lookup portal; use the JSON files when you want to filter or drive tooling from the catalog instead of reading markdown. The repository carries explicit LGPD and ethics warnings — many linked portals expose personal data, and misuse is on the user.

## Typical use cases

### Clone the catalog and list its categories

Get the dataset locally and see the 39 top-level groupings.

```
git clone https://github.com/osintbrazuca/osint-brazuca
cd osint-brazuca
jq -r '.sources[].categoria' data/sources.json | sort -u
```

Outcome: prints the deduplicated category names (social benefits, missing persons, court records, etc.), giving the map of what the catalog covers before you query it.

### Find every source that accepts a given identifier type

Filter the canonical catalog by the input vocabulary.

```
jq -r '.sources[] | select(.input | index("cpf")) | .id' data/sources.json
```

Outcome: lists the source ids of every portal that can be queried with a CPF; swap `"cpf"` for `"cnpj"` or another term from `data/taxonomy.json` to pivot the catalog by identifier, and use `.links[].url` in the select to go straight to the portal URLs.

### Flatten and filter all catalogued URLs

Work from the link-per-record index instead of the grouped catalog.

```
jq -r '.links[].url' data/index.json | grep -E '\.gov\.br' | sort -u
```

Outcome: prints the deduplicated set of Brazilian government URLs in the catalog — a ready-made feed for scoping, monitoring or blocklist building, without parsing the markdown handbook.

> No corpus evidence — derived from upstream documentation.
