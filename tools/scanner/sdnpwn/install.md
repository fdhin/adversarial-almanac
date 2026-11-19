---
tool: "sdnpwn"
build-system: "scripts"
upstream: "https://github.com/smythtech/sdnpwn"
recipe: "blackarch"
source: "https://github.com/smythtech/sdnpwn"
license: "custom:unknown"
blackarch-pkgver: "85.0080574"
---

# Installing sdnpwn

> An SDN penetration testing toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sdnpwn) (version `85.0080574`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sdnpwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/smythtech/sdnpwn) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `netifaces`, `python-openflow`, `scapy`, `scipy`, `tabulate`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bridge-utils`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sdnpwn]] · [[BlackArch Tools]]

