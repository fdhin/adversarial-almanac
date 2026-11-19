---
name: "sourcemapper"
categories:
  - blackarch-webapp
version: "40.f739bd5"
homepage: "https://github.com/denandz/sourcemapper"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-08-02"
build-system: go
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "denandz/sourcemapper"
github-stars: 1469
github-pushed: "2026-07-24"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# sourcemapper

> Rebuild the original JavaScript source tree from a webpack/browserify sourcemap file — local or fetched from a URL.

## What it's for

When a deployed site ships a `.map` sourcemap alongside its bundled JS (or embeds one as a
base64 `sourceMappingURL` blob), sourcemapper (denandz, Go) reconstructs the pre-build source:
it parses the map's `sources` entries and writes each original file, recreating the directory
tree the bundler consumed. This routinely exposes unminified application logic, comments, and
sometimes secrets — a first stop in client-side review. Exactly one of `-url`, `-jsurl` or
`-dir` is accepted per run; `-output` is always required. It only extracts what the map already
contains — it cannot recover sources from maps that were stripped.

## Typical use cases

### Extract sources from a hosted .map file

```
sourcemapper -url https://target.example.com/static/js/app.4f3e9d2.js.map -output appsrc
```

Outcome: the map is downloaded and parsed (entry count reported), and the original files are
written under `appsrc/`, mirroring the build-time paths (`webpack:/...` and the like); the tree
is then greppable like normal source.

### Extract from a locally saved map, or a base64 inline blob

```
sourcemapper -url app.js.map -output appsrc
```

Outcome: same extraction from a file on disk — for inline `sourceMappingURL=data:application/json;base64,...`
blobs, decode the blob to a file first, then pass the path here.

### Recover the map through the JavaScript entry point

```
sourcemapper -jsurl https://target.example.com/static/js/app.js -output appsrc -insecure
```

Outcome: the JS file is fetched, its sourcemap reference is resolved and pulled, and sources are
written to `appsrc`; `-insecure` tolerates broken TLS on the target host.

### Batch a directory of saved maps

```
sourcemapper -dir ./maps -output ./src
```

Outcome: every `.map` under `./maps` is processed recursively into `./src`; broken or empty
maps are logged and skipped without aborting the batch.

No corpus evidence — derived from upstream documentation (README).
