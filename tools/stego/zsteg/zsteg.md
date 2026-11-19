---
name: "zsteg"
categories:
  - blackarch-stego
version: "v0.2.14.r0.gb75b578"
homepage: "https://github.com/zedxff/zsteg"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-31"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "zed-0xff/zsteg"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 1632
github-pushed: "2026-01-28"
github-archived: false
github-issues: 6
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# zsteg

> Detect LSB-steganography and hidden data in PNG and BMP files.

## What it's for

zsteg, by zed-0xff, is a Ruby command-line detector for data hidden in PNG
and BMP images. It sweeps LSB steganography across channels, bit depths and
pixel iteration orders, and flags anything recognizable it finds: ASCII text,
zlib-compressed streams, and signatures of known tools such as OpenStego,
Camouflage and wbStego. Each result line names the exact extraction
parameters that produced it (e.g. `b3,rgb,lsb,xy`), which feed directly into
a targeted re-run or extraction. It detects and extracts; it does not embed.

## Typical use cases

### Run every known detection method

Do the full sweep over a suspicious image.

```
zsteg -a cover.png
```

Outcome: prints one line per promising parameter combination, e.g. `b3,rgb,lsb,xy .. text: "..."` or `.. zlib: data="%PDF-1.4..."` — the channel/bits/order prefix of any hit is the coordinate system for everything else.

### Test one specific extraction

Check a particular bits/channel/order combination directly.

```
zsteg cover.png 2b,b,lsb,xy
```

Outcome: analyses only the named plane (here bit 2, blue channel, LSB-first, xy order — shorthand for `--bits 2 --channel b --lsb --order xy`), printing any text, file-type or compressed-data match it yields.

### Extract a found payload

Save the data behind a hit reported by a scan.

```
zsteg -E "b1,rgb,lsb,xy" cover.png
```

Outcome: extracts the payload found at those exact coordinates (the name string is the prefix of the matching scan line) and writes it to a file instead of just summarizing its type.

*No corpus evidence — derived from upstream documentation.*
