---
tool: "hashid"
build-system: "scripts"
upstream: "https://github.com/psypanda/hashID"
recipe: "blackarch"
nix-package: "hashid"
aur-package: "hashid"
source: "https://github.com/psypanda/hashID"
license: "GPL3"
blackarch-pkgver: "397.7e8473a"
kali-package: "hashid"
kali-version: "3.1.4-5"
---

# Installing hashid

> Software to identify the different types of hashes used to encrypt data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashid) (version `397.7e8473a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hashid    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install hashid    # Kali package version 3.1.4-5
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/psypanda/hashID.git
cd hashID
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[crypto]] · [[hashid]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hashid    # ad-hoc run, no install
nix profile install nixpkgs#hashid   # or persist it
```
