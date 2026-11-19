---
name: "depix"
categories:
  - blackarch-misc
version: "38.f7d1850"
homepage: "https://github.com/spipm/Depixelization_poc"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-20"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "spipm/Depixelization_poc"
github-stars: 4534
github-pushed: "2026-04-14"
github-archived: true
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# depix

> Recovers plaintext from screenshots in which text was pixelized with a linear box filter.

## What it's for

Depix reverses naive pixelization by exploiting that a linear box filter maps each pixel block independently: a block can be identified by matching it against blocks of known plaintext rendered the same way. You supply the pixelized cutout (`-p`) and a "search image" (`-s`) — a De Bruijn sequence of the expected characters, screenshotted from an editor with the same font, size and colors as the original image. Uniquely matched blocks are taken as correct; remaining multi-match blocks are resolved using the geometric distances between neighbours. It works best on short secrets such as pixelized passwords, requires matching font settings (or enough surrounding plaintext to use the original itself as search image), and fails once the image has been lossily recompressed. The project began as a GitHub proof-of-concept and has since been renamed and moved: the GitHub repository now lives at spipm/Depixelization_poc and development continues on Codeberg; alternative tools include UnRedacter and DepixHMM.

## Typical use cases

### Recover pixelized text with a bundled search image

```
python3 depix.py \
    -p /path/to/pixelated.png \
    -s images/searchimages/debruinseq_notepad_Windows10_closeAndSpaced.png \
    -o /path/to/output.png
```

Outcome: writes output.png with the recovered text — resolved blocks drawn directly, ambiguous blocks rendered as the average of their candidates. The search image named here ships in the repository (Notepad, Windows 10); build your own with the editor and font that produced the original screenshot.

### Recover text pixelized in linear sRGB

```
python3 depix.py \
    -p /path/to/pixelated.png \
    -s images/searchimages/debruin_sublime_Linux_small.png \
    --backgroundcolor 40,41,35 \
    --averagetype linear
```

Outcome: re-runs the matching with linear-sRGB averaging and filters the given R,G,B editor background color, which is what editors such as GIMP and Sublime need for clean matches.

### Check that the pixel blocks were cut correctly

```
python3 tool_show_boxes.py -p /path/to/pixelated.png
```

Outcome: visualizes the pixel blocks the detector found; mangled or overlapping boxes mean the cutout must be tighter — Depix assumes blocks aligned to integer boundaries.

### Create a pixelized test image

```
python3 tool_gen_pixelated.py -i /path/to/original.png -o pixelated.png
```

Outcome: produces a known pixelized input from a plaintext image, letting you validate a candidate search image end-to-end before relying on it.
