---
tool: "peass"
build-system: "scripts"
upstream: "https://github.com/carlospolop/PEASS-ng"
recipe: "blackarch"
source: "https://github.com/carlospolop/PEASS-ng"
license: "MIT"
blackarch-pkgver: "20261029.a596a9f3.r0.g712a4f4"
provides: ["linpeas", "linpeas_fat"]
kali-package: "peass"
kali-version: "20261019.f07b7f97-0kali1"
---

# Installing peass

> Privilege Escalation Awesome Scripts SUITE (with colors).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/peass) (version `20261029.a596a9f3.r0.g712a4f4`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S peass    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install peass    # Kali package version 20261019.f07b7f97-0kali1
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/carlospolop/PEASS-ng.git
cd PEASS-ng
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `wget`

## Installed layout (per recipe)

- Commands: `/usr/bin/linpeas`, `/usr/bin/linpeas_fat`
- Data: `/usr/share/peass`, `/usr/share/windows`

Backlinks: [[scanner]] · [[peass]] · [[BlackArch Tools]]

