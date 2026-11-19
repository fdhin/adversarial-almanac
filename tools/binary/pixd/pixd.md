---
name: "pixd"
categories:
  - blackarch-binary
version: "9.f49add4"
homepage: "https://github.com/FireyFly/pixd"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-03-02"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "FireyFly/pixd"
github-stars: 564
github-pushed: "2024-02-26"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pixd

> Renders binary files as a grid of coloured squares — a visual hexdump that makes structure, padding, and embedded data jump out.

## What it's for

Use this when a hexdump is too dense to eyeball: locating embedded files inside a firmware image or disk dump, spotting encrypted-vs-compressed regions, comparing headers across samples, or judging overall file structure at a glance. Each octet becomes a coloured square on a 24-bit-colour terminal (the high nibble maps to a hue, the low nibble to brightness), so regular data forms visible bands and blocks. It reads files given as arguments or standard input, and the palette can be overridden with the `PIXD_COLORS` environment variable (256 whitespace-separated hex colours). It is a read-only visualization tool — it never modifies its input.

## Typical use cases

### Visualize a whole binary file

Render a file's contents as a colour map.

```
pixd firmware.bin
```

Outcome: prints the file as coloured squares, 64 octets per line by default; headers, padding, and embedded regions appear as distinct colour bands.

### Zoom in on an offset range

Display only a slice of a file, which is useful when an embedded format starts at a known offset.

```
pixd -r0x1000+0x200 foo.bin
```

Outcome: shows the 512-byte range of `foo.bin` starting at offset `0x1000` (`start+count` syntax; `start-end` also works), letting you inspect an embedded archive or header without paging through the whole file.

### Compare headers of several samples

Show the first bytes of each of several files with a heading per file.

```
pixd -r-0x400 sample*.bin
```

Outcome: prints the first 1024 bytes of every `sample*.bin` with a heading above each, so matching or diverging header layouts can be compared visually side by side.

### Use as a filter in a pipeline

Pipe data through pixd instead of giving it files.

```
curl -s http://example.com | pixd | less -R
```

Outcome: renders the downloaded bytes as colour squares as they stream through; `less -R` keeps the colour escape sequences intact while paging.

### Widen the display

Change how many octets are printed per line.

```
pixd -w 128 bigfile.img
```

Outcome: prints the file 128 octets per line instead of the default 64, making large-scale structure (e.g. alternating regions in a disk image) easier to see.

*No corpus evidence — derived from upstream documentation.*
