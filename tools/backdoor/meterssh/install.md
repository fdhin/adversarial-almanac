---
tool: "meterssh"
build-system: "scripts"
upstream: "https://github.com/trustedsec/meterssh"
recipe: "blackarch"
source: "https://github.com/trustedsec/meterssh"
license: "custom:unknown"
blackarch-pkgver: "18.9a5ed19"
provides: ["meterssh-monitor"]
---

# Installing meterssh

> A way to take shellcode, inject it into memory then tunnel whatever port you want to over SSH to mask any type of communications as a normal SSH connection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/meterssh) (version `18.9a5ed19`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S meterssh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/trustedsec/meterssh) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `metasploit`
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/meterssh-monitor`

Backlinks: [[backdoor]] · [[meterssh]] · [[BlackArch Tools]]

