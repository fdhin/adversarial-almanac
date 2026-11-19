---
tool: "nasty"
build-system: "cmake"
upstream: "https://github.com/folkertvanheusden/nasty"
recipe: "kali-dsc"
nix-package: "nasty"
version: "0.6+git20220929.9830b7a-3+b1"
---

# Installing nasty

> tool which helps you to recover your GPG passphrase

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/nasty) (version `0.6+git20220929.9830b7a-3+b1`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install nasty
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss nasty`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#nasty    # ad-hoc run, no install
nix profile install nixpkgs#nasty   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/folkertvanheusden/nasty
cd nasty
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Build dependencies (from source .dsc)

- `cmake`, `debhelper-compat`, `libgpgme-dev`, `pkgconf`
- Runtime deps: see [pkg.kali.org/pkg/nasty](https://pkg.kali.org/pkg/nasty)

Backlinks: [[forensic]] · [[nasty]] · [[Kali Gap Analysis]]
