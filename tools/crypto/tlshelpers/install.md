---
tool: "tlshelpers"
build-system: "scripts"
upstream: "https://github.com/hannob/tlshelpers"
recipe: "blackarch"
source: "https://github.com/hannob/tlshelpers"
license: "Unlicense"
blackarch-pkgver: "43.2b0ce92"
---

# Installing tlshelpers

> A collection of shell scripts that help handling X.509 certificate and TLS issues.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tlshelpers) (version `43.2b0ce92`, license Unlicense).

## Arch / BlackArch

```bash
sudo pacman -S tlshelpers    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hannob/tlshelpers) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `der-ascii`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[tlshelpers]] · [[BlackArch Tools]]

