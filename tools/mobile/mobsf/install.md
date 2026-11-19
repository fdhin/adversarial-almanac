---
tool: "mobsf"
build-system: "scripts"
upstream: "https://github.com/MobSF/Mobile-Security-Framework-MobSF"
recipe: "blackarch"
nix-package: "mobsf"
source: "https://github.com/MobSF/Mobile-Security-Framework-MobSF"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v4.6.4.r2.gf85399f"
---

# Installing mobsf

> An intelligent, all-in-one open source mobile application (Android/iOS) automated pen-testing framework capable of performing static, dynamic analysis and web API testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mobsf) (version `v4.6.4.r2.gf85399f`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S mobsf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`, `setuptools-scm`, `xmlsec`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gunicorn`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[mobsf]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mobsf    # ad-hoc run, no install
nix profile install nixpkgs#mobsf   # or persist it
```
