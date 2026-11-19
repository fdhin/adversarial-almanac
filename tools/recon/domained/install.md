---
tool: "domained"
build-system: "scripts"
upstream: "https://github.com/TypeError/domained"
recipe: "blackarch"
source: "https://github.com/TypeError/domained"
license: "GPL3"
blackarch-pkgver: "80.d9d079c"
---

# Installing domained

> Multi Tool Subdomain Enumeration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/domained) (version `80.d9d079c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S domained    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/TypeError/domained) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests`
- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `amass`
  - `knock`
  - `ldns`
  - `massdns`
  - `python`
  - `recon-ng`
  - `subbrute`
  - `subfinder`
  - `sublist3r`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[domained]] · [[BlackArch Tools]]

