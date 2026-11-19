---
tool: "tor-autocircuit"
build-system: "scripts"
upstream: "http://www.thesprawl.org/projects/tor-autocircuit/"
recipe: "blackarch"
source: "http://www.thesprawl.org/projects/tor-autocircuit/"
license: "custom"
blackarch-pkgver: "0.2"
---

# Installing tor-autocircuit

> Tor Autocircuit was developed to give users a finer control over Tor circuit creation. The tool exposes the functionality of TorCtl library which allows its users to control circuit length, speed, geolocation, and other parameters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tor-autocircuit) (version `0.2`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S tor-autocircuit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.thesprawl.org/projects/tor-autocircuit/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `tor`
- ⚠️ Python 2 only deps: python2-elixir, python2-geoip, python2-pytorctl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[tor-autocircuit]] · [[BlackArch Tools]]

