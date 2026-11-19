---
tool: "redirectpoison"
build-system: "scripts"
upstream: "http://www.hackingexposedvoip.com/"
recipe: "blackarch"
source: "http://www.hackingexposedvoip.com/tools/hack_library.tar.gz"
license: "GPL"
blackarch-pkgver: "1.1"
---

# Installing redirectpoison

> A tool to poison a targeted issuer of SIP INVITE requests with 301 (i.e. Moved Permanently) redirection responses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/redirectpoison) (version `1.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S redirectpoison    # needs the [blackarch] repo enabled
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
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[redirectpoison]] · [[BlackArch Tools]]

