---
name: "cook"
categories:
  - blackarch-automation
version: "v2.3.0.r0.gf72413d"
homepage: "https://github.com/giteshnxtlvl/cook"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-07"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "giteshnxtlvl/cook"
github-stars: 1383
github-pushed: "2026-03-06"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# cook

> Wordlist generator that composes columns, files, and functions into one pattern expression.

## What it's for

cook builds wordlists by describing them as a pattern of columns: every column is a literal list, a range, a local file, a URL, or a function, and cook emits the full permutation cross-product — optionally appending line-by-line instead of permuting (`-append`), or repeating values (`*`, `**` operators) for null-payload fuzzing. Results can be pushed through methods on single columns or the final output: case changes, leet, splitting on naming conventions (`smart`, `smartjoin`), base64/hex/URL encodings, MD5/SHA hashes, and JSON field extraction, chainable (`md5.b64e.urle`) or parallel (`md5,sha1,sha256`). It bundles the cook-ingredients collection — SecLists, Assetnote, fuzzdb and more — addressable by keyword, and saves your own ingredients in `my.yaml` (v2.2+ stores them under `~/.config/cook/cook-ingredients`). Reach for it when the target's password or path structure is known ("company + separator + file", "basename of every .zip in a JSON dump"); it generates only, but pipes cleanly into ffuf, dnsx, and similar tools.

## Typical use cases

### Compose a pattern from named parameters

Name each column, then reference them in the pattern.

```
cook -start example,corp -sep _,- -end backup.zip,secret.txt / start sep end
```

Outcome: prints the 8-line cross-product (2 starts × 2 separators × 2 endings) such as `example_backup.zip`, `corp-secret.txt`; parameters must appear in the pattern or nothing prints.

### Load a column from a local file or URL

Any file — or fetched URL — becomes a column.

```
cook -f: https://example.com/wordlist.txt f
```

Outcome: fetches the URL and uses its lines as column `f` (same syntax with a local path: `-f: live.txt f`), printing each line once as the pattern output.

### Join two wordlists line-by-line

Paste files together instead of exploding them into a cross product.

```
cook -l: live.txt -p: payloads.txt l / p -append 2
```

Outcome: pairs line 1 of `live.txt` with line 1 of `payloads.txt`, line 2 with line 2, and so on — useful for building user:password or host:port pairs without combinatorial growth.

### Break naming conventions into component words

Split camelCase and delimited identifiers into their parts.

```
cook adminNew,admin_old -m smart
```

Outcome: applies the `smart` method to the output, yielding `admin`, `New`, `admin`, `old` — the component vocabulary hidden inside identifiers; `smartjoin[:_]` instead re-joins parts with a separator, and `smartjoin[c:_]` also capitalizes each part.

### Distill a JSON dataset into a wordlist

One line from raw dataset to processed wordlist.

```
cook -z shub_zip_files z.json[path].fb.sortu.smartjoin[c:_]
```

Outcome: loads the `shub_zip_files` dataset (a saved JSON-lines URL of GitHub path records), extracts the `path` field, takes each file's basename, sorts unique, and re-joins naming conventions with capitalization — a usable permutation list from otherwise useless JSON.

### Save your own ingredient for reuse

Keep a project-specific wordlist under a keyword.

```
cook add my_paths=https://example.com/paths.json in files
```

Outcome: records `my_paths` in `my.yaml` (creating it if needed); afterwards `cook -z my_paths z` uses it as a column, and `cook delete my_paths` removes it.

*No corpus evidence — derived from upstream documentation.*
