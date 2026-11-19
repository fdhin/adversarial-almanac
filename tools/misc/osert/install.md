---
tool: "osert"
build-system: "scripts"
upstream: "https://github.com/noraj/OSCP-Exam-Report-Template-Markdown"
recipe: "blackarch"
source: "https://github.com/noraj/OSCP-Exam-Report-Template-Markdown"
license: "MIT"
blackarch-pkgver: "100.4eef9b9"
---

# Installing osert

> Markdown Templates for Offensive Security exam reports.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/osert) (version `100.4eef9b9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S osert    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/noraj/OSCP-Exam-Report-Template-Markdown) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `haskell-pandoc`
  - `p7zip`
  - `ruby`
  - `texlive-core`
  - `texlive-fontsextra`
  - `texlive-fontsrecommended`
  - `texlive-latexextra`
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/haskell-pandoc`

Backlinks: [[misc]] · [[osert]] · [[BlackArch Tools]]

