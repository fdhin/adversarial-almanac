---
tool: "rdp-cipher-checker"
build-system: "scripts"
upstream: "https://labs.f-secure.com/tools/rdp-cipher-checker/"
recipe: "blackarch"
source: "https://labs.f-secure.com/tools/rdp-cipher-checker/"
license: "BSD"
blackarch-pkgver: "0.1"
---

# Installing rdp-cipher-checker

> Enumerate the encryption protocols supported by the server and the cipher strengths supported using native RDP encryption.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rdp-cipher-checker) (version `0.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S rdp-cipher-checker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://labs.f-secure.com/tools/rdp-cipher-checker/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[rdp-cipher-checker]] · [[BlackArch Tools]]

