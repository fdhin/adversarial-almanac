---
tool: "http-traceroute"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/107167/Traceroute-Like-HTTP-Scanner.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/107167/Traceroute-Like-HTTP-Scanner.html"
license: "custom:unknown"
blackarch-pkgver: "0.5"
---

# Installing http-traceroute

> This is a python script that uses the Max-Forwards header in HTTP and SIP to perform a traceroute-like scanning functionality.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/http-traceroute) (version `0.5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S http-traceroute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/107167/Traceroute-Like-HTTP-Scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[http-traceroute]] · [[BlackArch Tools]]

