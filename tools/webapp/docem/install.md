---
tool: "docem"
build-system: "scripts"
upstream: "https://github.com/whitel1st/docem"
recipe: "blackarch"
source: "https://github.com/whitel1st/docem"
license: "custom:unknown"
blackarch-pkgver: "21.59db436"
---

# Installing docem

> Uility to embed XXE and XSS payloads in docx,odt,pptx,etc (OXML_XEE on steroids).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/docem) (version `21.59db436`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S docem    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/whitel1st/docem) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `lxml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[docem]] · [[BlackArch Tools]]

