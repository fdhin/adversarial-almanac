---
tool: "zeek"
build-system: "node"
upstream: "https://github.com/zeek/zeek"
recipe: "blackarch"
nix-package: "zeek"
aur-package: "zeek"
source: "https://github.com/zeek/zeek"
license: "BSD-3-Clause"
blackarch-pkgver: "v9.1.0.dev.r465.g1638b40"
---

# Installing zeek

> A powerful network analysis framework that is much different from the typical IDS you may know.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zeek) (version `v9.1.0.dev.r465.g1638b40`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S zeek    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/zeek/zeek) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`, `nodejs`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bison`
  - `crypto++`
  - `flex`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl-1.0`
  - `perl`
  - `python`
  - `ruby`
  - `swig`
  - `zeromq`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure \
    --prefix=/usr/share/zeek \
    --disable-broker-tests \
    --disable-cpp-tests \

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[zeek]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#zeek    # ad-hoc run, no install
nix profile install nixpkgs#zeek   # or persist it
```
