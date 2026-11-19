---
tool: "suricata"
build-system: "rust"
upstream: "https://suricata.io/download/"
recipe: "blackarch"
nix-package: "suricata"
aur-package: "suricata"
source: "https://suricata.io/download/"
license: "GPL2"
blackarch-pkgver: "6.0.20"
---

# Installing suricata

> An Open Source Next Generation Intrusion Detection and Prevention Engine.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/suricata) (version `6.0.20`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S suricata    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary:** check [upstream releases](https://suricata.io/download//releases).

**B. From source (needs `cargo`):**

**Source:** download the release from the [upstream site](https://suricata.io/download/) (the recipe uses a versioned archive).

```bash
cargo build --release
# binary in target/release/
```

## Dependencies (from recipe)

- **Python (pip)**: `magic`
- **Build tools**: `cargo`
- **System libraries** (Arch name — translate for your distro):
  - `file`
  - `geoip`
  - `jansson`
  - `libcap-ng`
  - `libmaxminddb`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libnetfilter_log`
  - `libnetfilter_queue`
  - `libnfnetlink`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libyaml`
  - `lua`
  - `luajit`
  - `nss` — Debian libnss3-dev / Fedora nss-devel
  - `pcre`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var \
    HAVE_PYTHON_CONFIG=/usr/bin/python3 --enable-gccprotect \
    --enable-nfqueue --enable-nflog --enable-luajit --enable-geoip \
    --disable-coccinelle --enable-old-barnyard2 --with-pic \
    --enable-python

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[suricata]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#suricata    # ad-hoc run, no install
nix profile install nixpkgs#suricata   # or persist it
```
