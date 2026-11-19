---
tool: "teardown"
build-system: "scripts"
upstream: "http://www.hackingexposedvoip.com/"
recipe: "blackarch"
source: "http://www.hackingexposedvoip.com/tools/hack_library.tar.gz"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing teardown

> Command line tool to send a BYE request to tear down a call.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/teardown) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S teardown    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO http://www.hackingexposedvoip.com/tools/hack_library.tar.gz
tar xf hack_library.tar.gz
cd hack_library
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[teardown]] · [[BlackArch Tools]]

