---
tool: "grabber"
build-system: "scripts"
upstream: "http://rgaucher.info/beta/grabber/"
recipe: "blackarch"
source: "http://rgaucher.info/beta/grabber/Grabber.zip"
license: "BSD"
blackarch-pkgver: "0.1"
provides: ["grabber-scanner"]
---

# Installing grabber

> A web application scanner. Basically it detects some kind of vulnerabilities in your website.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/grabber) (version `0.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S grabber    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO http://rgaucher.info/beta/grabber/Grabber.zip
tar xf Grabber.zip
cd Grabber
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python`

## Installed layout (per recipe)

- Commands: `/usr/bin/grabber-scanner`

Backlinks: [[webapp]] · [[grabber]] · [[BlackArch Tools]]

