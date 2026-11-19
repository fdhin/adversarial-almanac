---
name: "forensics-colorize"
categories:
  - forensics
version: "1.1-7"
homepage: "https://github.com/jessek/colorize"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: other
meta-checked: "2026-11-09"
github-repo: "jessek/colorize"
github-stars: 10
github-pushed: "2021-09-29"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# forensics-colorize

> Render large binary files such as disk images as bitmap graphics so differences and structure are visible at a glance.

## What it's for

forensics-colorize is Jesse Kornblum's pair of small C utilities, `colorize` and `filecompare`, for visually comparing large binary files where `diff` is useless. `filecompare` reads two files block by block and writes one byte per block to stdout — one value where the blocks match, another where they differ (or a grayscale ramp with `-t`). `colorize` turns any file's raw bytes into a `.bmp` image, so the `filecompare` output becomes a picture in which changed regions stand out immediately. The tools do not parse or understand file contents — the comparison is purely visual, which is exactly what makes it work on multi-gigabyte filesystem images and memory dumps. Same-size files are expected; unequal sizes are reported.

## Typical use cases

### Compare two filesystem images block by block

Produce a compact one-byte-per-block difference stream of two images.

```
filecompare -b 4k image1.raw image2.raw > compare.out
```

Outcome: `compare.out` holds one byte per 4096-byte block (default block size is 512): the match value for identical blocks, the differ value for changed ones, padding after the shorter file ends. Standard error reports the file sizes; feed the stream to `colorize` next. Add `-t` for a grayscale ramp of how much each block differs instead of a binary match/differ value.

### Turn the difference stream into an image

Render the comparison output as a bitmap.

```
colorize -w 100 compare.out
```

Outcome: writes `compare.out.bmp` (input name plus `.bmp`), 100 pixels wide by default — open it in any image viewer; matching regions form a uniform field and altered regions appear as contrasting bands whose size approximates the extent of change.

### Visualize the internal structure of a single file

Render one file's bytes directly to spot embedded regions, slack or padding.

```
colorize -o memdump.raw
```

Outcome: writes `memdump.raw.bmp`; `-o` lays the image out horizontally instead of vertically (default), and `-d` flips the direction bytes flow, which can make internal boundaries easier to see.

*No corpus evidence — derived from upstream documentation.*
