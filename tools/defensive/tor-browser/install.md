---
tool: "tor-browser"
build-system: "scripts"
upstream: "https://download.torproject.org/tor-browser-for-desktop/"
recipe: "blackarch"
nix-package: "tor-browser"
source: "https://download.torproject.org/tor-browser-for-desktop/"
license: "MPL-2.0"
blackarch-pkgver: "16.0"
---

# Installing tor-browser

> Tor Browser Bundle: anonymous browsing using Firefox and Tor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tor-browser) (version `16.0`, license MPL-2.0).

## Arch / BlackArch

```bash
sudo pacman -S tor-browser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://download.torproject.org/tor-browser-for-desktop/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `dbus-glib`
  - `desktop-file-utils`
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `hunspell`
  - `icu`
  - `libevent`
  - `libvpx`
  - `libxt`
  - `mime-types`
  - `nss` — Debian libnss3-dev / Fedora nss-devel
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
  - `startup-notification`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/icons`

Backlinks: [[defensive]] · [[tor-browser]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#tor-browser    # ad-hoc run, no install
nix profile install nixpkgs#tor-browser   # or persist it
```
