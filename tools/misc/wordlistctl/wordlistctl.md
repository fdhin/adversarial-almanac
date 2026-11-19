---
name: "wordlistctl"
categories:
  - blackarch-misc
version: "0.9.4"
homepage: "https://github.com/BlackArch/wordlistctl"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "BlackArch/wordlistctl"
github-stars: 541
github-pushed: "2024-04-17"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wordlistctl

> Catalog-driven downloader maintained by the BlackArch team: fetch, decompress and search 6,400+ known wordlists from public sites, organised by group (usernames, passwords, discovery, fuzzing, misc).

*No corpus evidence — derived from upstream documentation.*

## What it's for

Acquiring wordlists instead of hunting them by hand: wordlistctl knows a catalogue of wordlist archives offered by various websites and fetches them by name or by group into a base directory (default `/usr/share/wordlists`), with parallel downloads and optional decompression. It also searches that catalogue — or your already-downloaded archives — so you can find the right list before committing to a multi-hundred-megabyte download. It does not generate, mutate or deduplicate wordlists; it is strictly a fetch/install/update/search front end.

## Typical use cases

### Fetch a whole group of wordlists

```
wordlistctl fetch -g passwords -d
```

Outcome: downloads every catalogued wordlist in the `passwords` group into `/usr/share/wordlists`, ten download workers in parallel by default; `-d` decompresses each archive on arrival and removes the compressed file. `-g` accepts `usernames`, `passwords`, `discovery`, `fuzzing` and `misc`; add `-b DIR` to target a different base directory.

### Fetch specific wordlists by name

```
wordlistctl fetch -l rockyou.txt -d -w 4
```

Outcome: fetches only the named wordlist(s) from the catalogue with the given worker count, decompressing on completion — the targeted form of a fetch when you already know which list you need.

### List what the catalogue offers

```
wordlistctl list -g discovery
```

Outcome: prints every catalogued wordlist in the group with the index and metadata you need to choose; append `-f INDEX` to fetch entries directly from the listing using the same fetch options.

### Search, then fetch the hits

```
wordlistctl search dirb -f 2 -d
```

Outcome: prints every catalogue entry matching the search term with its result index, then downloads result 2 (decompressing it) in the same run; add `-l` to search the archives already present under the base directory instead of the remote catalogue.
