---
tool: "dradis-ce"
build-system: "scripts"
upstream: "https://dradis.com/ce/"
recipe: "blackarch"
source: "https://dradis.com/ce/"
license: "GPL"
blackarch-pkgver: "v5.0.0.r892.g26c057b"
---

# Installing dradis-ce

> An open source framework to enable effective information sharing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dradis-ce) (version `v5.0.0.r892.g26c057b`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dradis-ce    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://dradis.com/ce/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `pkgconf`
- **System libraries** (Arch name — translate for your distro):
  - `libsass`
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `postgresql-libs` — Debian libpq-dev / Fedora libpq-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-erb`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/dradis-ce`

Backlinks: [[recon]] · [[dradis-ce]] · [[BlackArch Tools]]

