---
tool: "mptcp-abuse"
build-system: "scripts"
upstream: "https://github.com/Neohapsis/mptcp-abuse"
recipe: "blackarch"
source: "https://github.com/Neohapsis/mptcp-abuse.git"
license: "GPL2"
blackarch-pkgver: "6.b0eeb27"
provides: ["mptcp-fragmenter", "mptcp-scanner"]
---

# Installing mptcp-abuse

> A collection of tools and resources to explore MPTCP on your network. Initially released at Black Hat USA 2014.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mptcp-abuse) (version `6.b0eeb27`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mptcp-abuse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Neohapsis/mptcp-abuse.git
cd mptcp-abuse
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/mptcp-fragmenter`, `/usr/bin/mptcp-scanner`

Backlinks: [[networking]] · [[mptcp-abuse]] · [[BlackArch Tools]]

