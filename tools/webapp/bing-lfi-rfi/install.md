---
tool: "bing-lfi-rfi"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/121590/Bing-LFI-RFI-Scanner.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/121590/Bing-LFI-RFI-Scanner.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing bing-lfi-rfi

> Python script for searching Bing for sites that may have local and remote file inclusion vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bing-lfi-rfi) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bing-lfi-rfi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/121590/Bing-LFI-RFI-Scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[bing-lfi-rfi]] · [[BlackArch Tools]]

