---
tool: "massbleed"
build-system: "scripts"
upstream: "https://github.com/1N3/Sn1per"
recipe: "blackarch"
source: "https://github.com/1N3/Sn1per"
license: "custom:unknown"
blackarch-pkgver: "20.44b7e85"
---

# Installing massbleed

> SSL Vulnerability Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/massbleed) (version `20.44b7e85`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S massbleed    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/1N3/MassBleed.git
cd MassBleed
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `nmap`
  - `python`
  - `sslscan`
  - `testssl.sh`
  - `unicornscan`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[massbleed]] · [[BlackArch Tools]]

