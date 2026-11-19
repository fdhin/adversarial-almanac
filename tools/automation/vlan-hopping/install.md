---
tool: "vlan-hopping"
build-system: "scripts"
upstream: "https://github.com/nccgroup/vlan-hopping"
recipe: "blackarch"
source: "https://github.com/nccgroup/vlan-hopping"
license: "GPL3"
blackarch-pkgver: "21.a37ba4e"
---

# Installing vlan-hopping

> Easy 802.1Q VLAN Hopping

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vlan-hopping) (version `21.a37ba4e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S vlan-hopping    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/vlan-hopping) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `arp-scan`
  - `bash`
  - `dmidecode`
  - `ethtool`
  - `metasploit`
  - `screen`
  - `vlan`
  - `wireshark-cli`
  - `yersinia`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[vlan-hopping]] · [[BlackArch Tools]]

