---
tool: "moloch"
build-system: "node"
upstream: "https://github.com/aol/moloch"
recipe: "blackarch"
source: "https://github.com/aol/moloch"
license: "APACHE"
blackarch-pkgver: "0.11.3"
---

# Installing moloch

> An open source large scale IPv4 full PCAP capturing, indexing and database system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/moloch) (version `0.11.3`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S moloch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/aol/moloch) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `nodejs`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `elasticsearch`
  - `file`
  - `geoip`
  - `glib2`
  - `libnids`
  - `libpng`
  - `perl-json`
  - `perl-libwww`
  - `python2`
  - `util-linux`

## Exact build steps from the recipe

```bash
# Build the static version of yara
  cd "yara-$_yaraver"

  ./bootstrap.sh
  ./configure --enable-static --enable-magic --enable-cuckoo
  make

  # Build moloch
  cd "../moloch-$pkgver"

  ./configure --prefix=$_dataroot --bindir=/usr/bin \
    --with-yara="$srcdir/yara-$_yaraver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[moloch]] · [[BlackArch Tools]]

