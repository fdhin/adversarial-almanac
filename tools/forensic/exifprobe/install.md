---
tool: "exifprobe"
build-system: "make"
upstream: "https://github.com/hfiguiere/exifprobe"
recipe: "kali-dsc"
nix-package: "exifprobe"
version: "2.0.1+git20201230.eee65ff-3"
---

# Installing exifprobe

> read metadata from digital pictures

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/exifprobe) (version `2.0.1+git20201230.eee65ff-3`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install exifprobe
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss exifprobe`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#exifprobe    # ad-hoc run, no install
nix profile install nixpkgs#exifprobe   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/hfiguiere/exifprobe
cd exifprobe
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `xutils-dev`
- Runtime deps: see [pkg.kali.org/pkg/exifprobe](https://pkg.kali.org/pkg/exifprobe)

Backlinks: [[forensic]] · [[exifprobe]] · [[Kali Gap Analysis]]
