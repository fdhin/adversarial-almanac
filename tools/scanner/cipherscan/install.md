---
tool: "cipherscan"
build-system: "scripts"
upstream: "https://github.com/jvehent/cipherscan"
recipe: "blackarch"
source: "https://github.com/jvehent/cipherscan"
license: "OpenSSL"
blackarch-pkgver: "423.30bd239"
---

# Installing cipherscan

> A very simple way to find out which SSL ciphersuites are supported by a target.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cipherscan) (version `423.30bd239`, license OpenSSL).

## Arch / BlackArch

```bash
sudo pacman -S cipherscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jvehent/cipherscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cipherscan]] · [[BlackArch Tools]]

