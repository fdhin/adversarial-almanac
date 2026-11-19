---
tool: "burpsuite"
build-system: "scripts"
upstream: "https://portswigger.net/burp/releases"
recipe: "blackarch"
nix-package: "burpsuite"
aur-package: "burpsuite"
source: "https://github.com/PortSwigger/command-injection-attacker.git"
license: "custom"
blackarch-pkgver: "2026.10.1"
kali-package: "burpsuite"
kali-version: "2026.10-0kali1"
---

# Installing burpsuite

> An integrated platform for attacking web applications (community edition) + SHELLING plugin.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/burpsuite) (version `2026.10.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S burpsuite    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install burpsuite    # Kali package version 2026.10-0kali1
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PortSwigger/command-injection-attacker.git
cd command-injection-attacker
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `giflib`
  - `java-runtime`
  - `libxext`
  - `libxrender`
  - `libxtst`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/pixmaps`

Backlinks: [[webapp]] · [[burpsuite]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#burpsuite    # ad-hoc run, no install
nix profile install nixpkgs#burpsuite   # or persist it
```
