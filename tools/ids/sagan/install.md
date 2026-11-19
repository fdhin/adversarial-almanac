---
tool: "sagan"
build-system: "autotools"
upstream: "https://quadrantsec.com/sagan_log_analysis_engine/"
recipe: "blackarch"
source: "https://quadrantsec.com/sagan_log_analysis_engine/"
license: "GPL"
blackarch-pkgver: "2.0.2"
---

# Installing sagan

> A snort-like log analysis engine.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sagan) (version `2.0.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sagan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://quadrantsec.com/sagan_log_analysis_engine/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `geoip`
  - `geoip-database`
  - `geoip-database-extra`
  - `geoip2-database`
  - `json-c`
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libesmtp`
  - `libfastjson`
  - `liblognorm`
  - `libyaml`
  - `mariadb-clients`
  - `pcre`
  - `postgresql-libs` — Debian libpq-dev / Fedora libpq-devel
  - `sagan-rules`

## Exact build steps from the recipe

```bash
cd "$pkgname-v-$_pkgver"

  ./autogen.sh

  ./configure --enable-esmtp --disable-geoip --prefix=/usr --sysconfdir=/etc \
    --sbindir=/usr/bin --enable-system-strstr

  make

  sed "s:^var RULE_PATH.*$:var RULE_PATH /usr/share/sagan/rules/:" \
    etc/sagan.yaml > etc/sagan.yaml.new

  mv etc/sagan.yaml.new etc/sagan.yaml
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[ids]] · [[sagan]] · [[BlackArch Tools]]

