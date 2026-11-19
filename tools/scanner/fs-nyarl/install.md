---
tool: "fs-nyarl"
build-system: "scripts"
upstream: "http://www.fulgursecurity.com/en/content/fs-nyarl"
recipe: "blackarch"
source: "http://www.fulgursecurity.com/en/content/fs-nyarl"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing fs-nyarl

> A network takeover & forensic analysis tool - useful to advanced PenTest tasks & for fun and profit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fs-nyarl) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S fs-nyarl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.fulgursecurity.com/en/content/fs-nyarl) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `arp-scan`
  - `beef`
  - `chaosreader`
  - `dsniff`
  - `ettercap`
  - `foremost`
  - `hamster`
  - `macchanger`
  - `metasploit`
  - `mitmproxy`
  - `p0f`
  - `sslsplit`
  - `sslstrip`
  - `tcpdump`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[fs-nyarl]] · [[BlackArch Tools]]

