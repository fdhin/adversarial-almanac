---
tool: "find-dns"
build-system: "scripts"
upstream: "https://packetstormsecurity.com/files/132449/Find-DNS-Scanner.html"
recipe: "blackarch"
source: "https://packetstormsecurity.com/files/132449/Find-DNS-Scanner.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing find-dns

> A tool that scans networks looking for DNS servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/find-dns) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S find-dns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://packetstormsecurity.com/files/132449/Find-DNS-Scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[find-dns]] · [[BlackArch Tools]]

