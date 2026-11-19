---
tool: "levye"
build-system: "scripts"
upstream: "https://github.com/galkan/levye"
recipe: "blackarch"
source: "https://github.com/galkan/levye"
license: "custum"
blackarch-pkgver: "84.5406303"
---

# Installing levye

> A brute force tool which is support sshkey, vnckey, rdp, openvpn.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/levye) (version `84.5406303`, license custum).

## Arch / BlackArch

```bash
sudo pacman -S levye    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/galkan/levye) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `freerdp`
  - `openssh`
  - `openvpn`
  - `python2`
- ⚠️ Python 2 only deps: python2-paramiko (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[levye]] · [[BlackArch Tools]]

