---
name: "gg-images"
categories:
  - blackarch-social
version: "35.b2dd863"
homepage: "https://codeberg.org/nanoory/gg_images"
packaged-by: [blackarch]
blackarch-builddate: "2023-02-28"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# gg-images

> GUI downloader that fetches full-resolution profile photos of Gadu-Gadu (GG) messenger accounts by number from GG's public avatar server.

*No corpus evidence — derived from upstream documentation.*

## What it's for

GG (Gadu-Gadu) is a Polish instant messenger whose avatars display small in-app, but whose avatar server publicly serves the same photos at up to 800×800 with no login — `https://avatars.gg.pl/user,<number>/s,800x800`. This Go/Fyne desktop app automates that fetch: enter a single number (typed or loaded from file), a random number, a range, or all numbers, and each photo downloads as `<number>.png`. It exploits no vulnerability — it only eases access to publicly available data — and the bulk modes (range, all numbers) are explicitly warned against because of the request volume they generate. Typical OSINT use is correlating a GG number with a person via reverse image search or cross-platform account matching. The project lives on Codeberg and is licensed GPL-3.0-only; BlackArch packages it as `gg-images`.

## Typical use cases

### Fetch avatars through the GUI

Retrieve one or many profile photos by GG number.

```
gg-images
```

Outcome: opens the application window — navigate Download → One number (enter a number or load one from file), Random number, Range numbers, or All numbers; each photo is saved as `<number>.png` under `./gg_images_download/` relative to the directory the binary was launched from.
