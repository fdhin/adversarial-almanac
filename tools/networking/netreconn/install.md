---
tool: "netreconn"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/86076/NetReconn-Scanning-Tool-Collection-1.76.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/86076/NetReconn-Scanning-Tool-Collection-1.76.html"
license: "GPL2"
blackarch-pkgver: "1.78"
---

# Installing netreconn

> A collection of network scan/recon tools that are relatively small compared to their larger cousins.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netreconn) (version `1.78`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S netreconn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/86076/NetReconn-Scanning-Tool-Collection.76.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[netreconn]] · [[BlackArch Tools]]

