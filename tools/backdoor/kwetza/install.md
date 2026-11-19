---
tool: "kwetza"
build-system: "scripts"
upstream: "https://github.com/sensepost/kwetza"
recipe: "blackarch"
source: "https://github.com/sensepost/kwetza"
license: "GPL2"
blackarch-pkgver: "26.0e50272"
---

# Installing kwetza

> Python script to inject existing Android applications with a Meterpreter payload.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kwetza) (version `26.0e50272`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S kwetza    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sensepost/kwetza) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `android-apktool`
  - `metasploit`
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[kwetza]] · [[BlackArch Tools]]

