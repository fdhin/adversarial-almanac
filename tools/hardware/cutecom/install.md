---
tool: "cutecom"
build-system: "cmake"
upstream: "https://gitlab.com/cutecom/cutecom"
recipe: "kali-dsc"
nix-package: "cutecom"
aur-package: "cutecom"
version: "0.51.0-1.1"
---

# Installing cutecom

> Graphical serial terminal, like minicom

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/cutecom) (version `0.51.0-1.1`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install cutecom
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss cutecom`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#cutecom    # ad-hoc run, no install
nix profile install nixpkgs#cutecom   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://gitlab.com/cutecom/cutecom
cd cutecom
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Build dependencies (from source .dsc)

- `debhelper`, `cmake`, `qtbase5-dev`, `qt5-qmake`, `qtbase5-dev-tools`, `libqt5serialport5-dev`
- Runtime deps: see [pkg.kali.org/pkg/cutecom](https://pkg.kali.org/pkg/cutecom)

Backlinks: [[hardware]] · [[cutecom]] · [[Kali Gap Analysis]]
