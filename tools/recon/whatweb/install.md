---
tool: "whatweb"
build-system: "scripts"
upstream: "http://www.morningstarsecurity.com/research/whatweb"
recipe: "blackarch"
nix-package: "whatweb"
aur-package: "whatweb"
source: "http://www.morningstarsecurity.com/research/whatweb"
license: "GPL"
blackarch-pkgver: "v0.6.4.r0.gd279d93"
kali-package: "whatweb"
kali-version: "0.6.4-1"
---

# Installing whatweb

> Next generation web scanner that identifies what websites are running.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/whatweb) (version `v0.6.4.r0.gd279d93`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S whatweb    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install whatweb    # Kali package version 0.6.4-1
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/urbanadventurer/WhatWeb.git
cd WhatWeb
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-addressable`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[recon]] · [[whatweb]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#whatweb    # ad-hoc run, no install
nix profile install nixpkgs#whatweb   # or persist it
```
