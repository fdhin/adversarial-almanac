---
tool: "tls-prober"
build-system: "scripts"
upstream: "https://github.com/WestpointLtd/tls_prober"
recipe: "blackarch"
source: "https://github.com/WestpointLtd/tls_prober"
license: "MIT"
blackarch-pkgver: "286.72b1029"
---

# Installing tls-prober

> A tool to fingerprint SSL/TLS servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tls-prober) (version `286.72b1029`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tls-prober    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/WestpointLtd/tls_prober.git
cd tls_prober
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[tls-prober]] · [[BlackArch Tools]]

