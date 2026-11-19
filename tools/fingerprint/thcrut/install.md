---
tool: "thcrut"
build-system: "autotools"
upstream: "https://github.com/vanhauser-thc/THC-Archive/"
recipe: "blackarch"
source: "http://www.insecure.org/nmap/data/nmap-os-fingerprints"
license: "custom:unknown"
blackarch-pkgver: "1.2.5"
---

# Installing thcrut

> Network discovery and OS Fingerprinting tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thcrut) (version `1.2.5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S thcrut    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/vanhauser-thc/THC-Archive/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `pcre`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ $CARCH == "aarch64" ]]; then
    ./configure --prefix=/usr --host="$CARCH"
  else
    ./configure --prefix=/usr
  fi

  sed -i 's/#define size_t unsigned//g' config.h
  sed -i 's/#define gid_t int//g' config.h
  sed -i 's/#define uid_t int//g' config.h
  sed -i 's/#define pid_t int//g' config.h

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/thcrut`

Backlinks: [[fingerprint]] · [[thcrut]] · [[BlackArch Tools]]

