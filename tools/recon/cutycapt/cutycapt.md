---
name: "cutycapt"
categories:
  - blackarch-recon
version: "11.0"
homepage: "https://cutycapt.sourceforge.net/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-12-12"
build-system: cmake
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "hoehrmann/CutyCapt"
github-stars: 98
github-pushed: "2024-02-14"
github-archived: false
github-issues: 24
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cutycapt

> Render a web page in a WebKit engine and capture the exact output as PNG, SVG, PDF and more.

## What it's for

CutyCapt is a small cross-platform command-line utility by Bjoern Hoehrmann
that loads a URL in an embedded Qt WebKit/QtWebEngine browser and writes what
the browser renders to a vector or bitmap file — PNG, JPEG, TIFF, GIF, BMP,
SVG, PDF, PS and others. Use it to archive what a page actually looked like
when rendered (JavaScript executed, images loaded), to capture evidence
screenshots during web assessments, or to feed rendered pages into further
processing. It is a renderer, not a crawler: one invocation captures exactly
one URL. Being a Qt GUI application under the hood, it needs a working X
display — use a virtual framebuffer on servers.

## Typical use cases

### Capture a page render to a file

One URL in, one image out.

```
cutycapt --url=http://example.com/ --out=example.png
```

Outcome: renders example.com in the bundled engine and writes the full page
viewport to example.png. The output extension selects the format, so
`--out=report.pdf` or `--out=diagram.svg` produce a PDF/SVG instead; use
`--out-format=` to set it explicitly when the filename has no useful
extension.

### Capture on a headless server

Wrap the run in a virtual framebuffer so no display is needed.

```
xvfb-run --server-args="-screen 0, 1024x768x24" cutycapt --url=http://example.com/ --out=example.png
```

Outcome: CutyCapt runs against an in-memory X server, producing the same
render without any physical display — the standard shape for scheduled
screenshot jobs from cron or a shell on a remote box.

### Control viewport size and timing

Deal with slow or JavaScript-driven pages.

```
cutycapt --url=http://example.com/ --out=example.png --min-width=1280 --min-height=800 --max-wait=60000 --delay=2000
```

Outcome: the render is at least 1280x800 pixels, the tool waits no longer
than 60 seconds for the initial load and then a further 2 seconds after it
before writing the file — enough for late-running scripts to paint their
content into the capture.

No corpus evidence — derived from upstream documentation (project homepage
usage reference and packaged sources).
