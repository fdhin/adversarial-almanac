---
tool: "photon"
build-system: "scripts"
upstream: "https://github.com/s0md3v/Photon"
recipe: "blackarch"
nix-package: "photon"
aur-package: "photon"
source: "https://github.com/s0md3v/Photon"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v1.2.2.r50.gc8d2566"
---

# Installing photon

> Incredibly fast crawler which extracts urls, emails, files, website accounts and much more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/photon) (version `v1.2.2.r50.gc8d2566`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S photon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/s0md3v/Photon.git
cd Photon
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `tld`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[photon]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#photon    # ad-hoc run, no install
nix profile install nixpkgs#photon   # or persist it
```
