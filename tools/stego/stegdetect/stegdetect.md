---
name: "stegdetect"
categories:
  - blackarch-stego
version: "20.28a4f07"
homepage: "https://github.com/redNixon/stegdetect"
packaged-by: [blackarch]
blackarch-builddate: "2023-10-05"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "redNixon/stegdetect"
github-stars: 71
github-pushed: "2022-05-06"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stegdetect

> Classical statistical detector for steganographic content in JPEG images.

## What it's for

Stegdetect, developed by Niels Provos, is the classic automated detector for
hidden data embedded in JPEG images. It runs statistical tests against a file
and reports which steganographic scheme it matches — jsteg, jphide (Unix and
Windows), invisible secrets, outguess 01.3b, F5 (header analysis), appendX and
camouflage — with a confidence rating. It detects; it does not extract — the
companion `stegbreak` (shipped by the same project) performs dictionary
attacks against jsteg/jphide/outguess files. The original outguess.org site
is gone; the packaged repo is a mirror that compiles on modern systems, and
the detector's age shows: it covers JPEG-era tools and misses modern
image stego entirely.

## Typical use cases

### Scan images for steganographic schemes

Run the default detection over a set of JPEGs.

```
stegdetect *.jpg
```

Outcome: prints one verdict line per file naming the matched scheme and a confidence rating in stars (e.g. `image.jpg : jsteg(***)`), or `negative` where no known scheme matches.

### Check a single file for F5 embedding

Run the thorough F5 test instead of the quick header check.

```
stegdetect -tF image.jpg
```

Outcome: performs the slow F5 detection over the file and prints its verdict; `-tf` runs the simple header-comment variant instead. Wrap it in a `find`/`for` loop to sweep a directory of JPEGs one file at a time.

*No corpus evidence — derived from upstream documentation.*
