---
tool: "backdoorme"
build-system: "scripts"
upstream: "https://github.com/Kkevsterrr/backdoorme"
recipe: "blackarch"
source: "https://github.com/Kkevsterrr/backdoorme"
license: "custom:unknown"
blackarch-pkgver: "308.f9755ca"
---

# Installing backdoorme

> A powerful utility capable of backdooring Unix machines with a slew of backdoors.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/backdoorme) (version `308.f9755ca`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S backdoorme    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Kkevsterrr/backdoorme) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `netifaces`, `paramiko`, `pexpect`, `rpyc`, `scp`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `tk`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[backdoorme]] · [[BlackArch Tools]]

