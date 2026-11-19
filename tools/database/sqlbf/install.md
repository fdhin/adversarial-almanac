---
tool: "sqlbf"
build-system: "make"
upstream: "http://www.cqure.net/wp/sqlpat/"
recipe: "pentoo-ebuild"
version: "1.0.1-r1"
license: "GPL-2"
---

# Installing sqlbf

> SQLBF is a tool to audit mssql passwords strength offline

Recipe source of truth: Pentoo [`sqlbf.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/dev-db/sqlbf) (version `1.0.1-r1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask sqlbf
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss sqlbf`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/sqlpat/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `app-arch/unzip`, `dev-libs/openssl`

Backlinks: [[database]] · [[sqlbf]] · [[Pentoo Gap Analysis]]
