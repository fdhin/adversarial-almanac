---
tool: "fakedns"
build-system: "scripts"
upstream: "https://github.com/Crypt0s/FakeDns"
recipe: "blackarch"
source: "https://github.com/Crypt0s/FakeDns"
license: "GPL3"
blackarch-pkgver: "118.39609da"
---

# Installing fakedns

> A regular-expression based python MITM DNS server with correct DNS request passthrough and "Not Found" responses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fakedns) (version `118.39609da`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fakedns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Crypt0s/FakeDns.git
cd FakeDns
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[fakedns]] · [[BlackArch Tools]]

