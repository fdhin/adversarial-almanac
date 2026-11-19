---
name: "bmc-tools"
categories:
  - blackarch-forensic
version: "38.5a4cad3"
homepage: "https://github.com/ANSSI-FR/bmc-tools"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-05-02"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ANSSI-FR/bmc-tools"
github-stars: 699
github-pushed: "2026-04-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# bmc-tools

> Reconstruct RDP session screenshots from Windows bitmap cache artifacts.

## What it's for

bmc-tools (by the French ANSSI) parses the RDP bitmap cache that Windows
Terminal Services clients keep per user — `bcache*.bmc` and `cache????.bin`
files under the user profile — and rebuilds the cached screen tiles into
viewable images. Because RDP transmits the desktop as compressed tiles that
the client caches, these artifacts act as a partial recording of what was
displayed in remote sessions: reconnecting to a compromised host, viewed
documents, or the last states seen before collection. The tool decompresses
tiles, writes each one as an individual PNG, and can assemble a collage
aggregating all tiles from an input for quick eyeballing. It parses a
specific client-side artifact; it does not capture live sessions and
recovers only what the cache still holds.

## Typical use cases

### Extract all cached RDP tiles

Process a user's bitmap cache directory and dump every tile as an image.

```
bmc-tools -s /mnt/image/Users/user/AppData/Local/Microsoft/Terminal Server Client/Cache -d rdp_out
```

Outcome: decompresses every `bcache*.bmc`/`cache????.bin` entry found under
the source path and writes the individual tile bitmaps into `rdp_out/`,
ready to page through for recognizable screen content.

### Build a collage for quick review

Aggregate all extracted tiles into single overview images instead of
thousands of tiny files.

```
bmc-tools -s /mnt/image/Users/user/AppData/Local/Microsoft/Terminal Server Client/Cache -d rdp_out -b -w 64
```

Outcome: in addition to the individual tiles, writes collage bitmap(s)
lining up 64 tiles per row (`-w`, default 64) so a whole session cache can
be skimmed in a few images.

### Recover the older cache generation

Also extract the old-format bitmap data stored inside BMCache files.

```
bmc-tools -s bcache23.bmc -d rdp_out -o
```

Outcome: emits both current and legacy bitmap entries found in the file,
increasing coverage on older Windows caches.

### Split outputs per input for tool pipelines

Keep results separated per source file, in the layout KAPE-based workflows
expect.

```
bmc-tools -s cache_dir -d rdp_out -k
```

Outcome: output is split into separate folders per input file rather than
one merged directory, which keeps multi-user or multi-file collections
attributable.

*No corpus evidence — derived from upstream documentation.*
