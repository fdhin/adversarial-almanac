---
name: "shuji"
categories:
  - blackarch-decompiler
version: "0.8.0.r140.g922cfa6"
homepage: "https://github.com/paazmaya/shuji"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: node
license: MIT
meta-checked: "2026-11-09"
github-repo: "paazmaya/shuji"
github-stars: 1246
github-pushed: "2026-11-05"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 51
gh-activity-checked: "2026-11-09"
---

# shuji

> Reverses the minification step for web front-ends: reads JavaScript/CSS source maps and writes out the original source files they describe.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Deployed web assets are usually minified, but they frequently ship with source maps that name — and often embed — the pre-minification sources. shuji consumes those maps: given a `.map` file (or a minified file carrying an inline data-URI map) it reconstructs the original `.js` and `.css` files into an output directory. Reach for it when auditing a production bundle for which you have no repository access. It is a Node.js command-line tool and handles whole directory trees in one pass. Limits: if a map lacks `sourcesContent` and only links to originals that are no longer online, you get the file names and structure but not the code itself.

## Typical use cases

### Recover sources from a source map

```
shuji app.min.js.map -o recovered
```

Outcome: the original files named in the map's `sources` are written under `recovered/` (the default output directory is the current one). Run it on any `.map` picked up from a deployed site to get the pre-minification JavaScript or CSS back.

### Batch-process a directory of maps

```
shuji dist/ -o recovered -v
```

Outcome: every file under `dist/` matching the map filter (default `\.map$`) is processed in one run; `-v` prints each file as it is handled, which is the quick way to confirm nothing was skipped.

### Extract from inline source maps in minified bundles

```
shuji build/ -o recovered -M '\.min\.(js|css)$'
```

Outcome: `-M` replaces the match expression controlling which files are treated as maps, so minified bundles carrying embedded data-URI maps (no separate `.map` file) are read directly and their original sources extracted.

### Keep the original folder layout

```
shuji maps/ -o recovered -p
```

Outcome: `-p` (preserve) writes the recovered files following the source map's original directory structure instead of flattening them into the output directory, so relative imports between recovered files stay sensible.
