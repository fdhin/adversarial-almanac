---
name: "imagejs"
categories:
  - blackarch-binary
version: "56.a442f94"
homepage: "https://github.com/jklmnn/imagejs"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: cmake
license: GPL3
meta-checked: "2026-11-09"
github-repo: "jklmnn/imagejs"
github-stars: 900
github-pushed: "2022-06-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# imagejs

> Package JavaScript into valid, viewable image files (BMP, GIF, WebP, PNM, PGF) that still execute as scripts.

## What it's for

imagejs creates image/JavaScript polyglots: files that are valid, viewable
images and at the same time run as JavaScript when fetched and executed in a
script context. The motivating scenario is a site that accepts image uploads
but only executes scripts served from its own origin: a "profile picture"
built with imagejs can carry the code past that restriction and extend an
XSS foothold. It is just as useful defensively — serving one of these
polyglots through your own upload pipeline is a direct test of whether
content-type sniffing and upload validation catch the technique. The C
implementation supports gif, bmp, webp (beta), pnm and pgf outputs; its author
labels it a proof of concept, tested only with valid inputs, so do not feed it
untrusted data.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Package a script as a BMP polyglot

```
imagejs bmp payload.js
```

Outcome: writes `payload.js.bmp` — a valid bitmap that executes as JavaScript
in contexts where image-served content is interpreted as code.

### Embed code into an existing GIF

```
imagejs gif payload.js -i avatar.gif
```

Outcome: produces a GIF based on the supplied image carrying the script, so
the payload looks like an ordinary uploaded picture. The `-i` embedding also
works for bmp and webp output.

### Render the payload as a visible pixel line

```
imagejs bmp payload.js -l
```

Outcome: writes a BMP in which the embedded code appears as a line of pixels
when the image is opened in a viewer (BMP only) — a quick way to demonstrate
visually what a polyglot file hides.
