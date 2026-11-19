---
tool: "crackserver"
build-system: "scripts"
upstream: "https://github.com/averagesecurityguy/crack"
recipe: "blackarch"
source: "https://github.com/averagesecurityguy/crack"
license: "custom"
blackarch-pkgver: "33.e5763ab"
provides: ["crack-standalone"]
---

# Installing crackserver

> An XMLRPC server for password cracking.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crackserver) (version `33.e5763ab`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S crackserver    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/averagesecurityguy/crack.git
cd crack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `hashcat`
  - `ike-scan`
  - `john`
  - `pyrit`
  - `python2`
  - `rcracki_mt`

## Installed layout (per recipe)

- Commands: `/usr/bin/crack-standalone`

Backlinks: [[cracker]] · [[crackserver]] · [[BlackArch Tools]]

