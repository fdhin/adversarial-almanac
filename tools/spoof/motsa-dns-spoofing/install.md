---
tool: "motsa-dns-spoofing"
build-system: "scripts"
upstream: "https://github.com/waytoalpit/ManOnTheSideAttack-DNS-Spoofing"
recipe: "blackarch"
source: "https://github.com/waytoalpit/ManOnTheSideAttack-DNS-Spoofing"
license: "custom:unknown"
blackarch-pkgver: "2.6ac6980"
provides: ["motsa-dnsdetect", "motsa-dnsinject"]
---

# Installing motsa-dns-spoofing

> ManOnTheSideAttack-DNS Spoofing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/motsa-dns-spoofing) (version `2.6ac6980`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S motsa-dns-spoofing    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/waytoalpit/ManOnTheSideAttack-DNS-Spoofing.git
cd ManOnTheSideAttack-DNS-Spoofing
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/motsa-dnsdetect`, `/usr/bin/motsa-dnsinject`

Backlinks: [[spoof]] · [[motsa-dns-spoofing]] · [[BlackArch Tools]]

