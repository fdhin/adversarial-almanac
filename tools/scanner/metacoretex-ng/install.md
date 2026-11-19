---
tool: "metacoretex-ng"
build-system: "scripts"
upstream: "http://metacoretex-ng.sourceforge.net"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-2"
---

# Installing metacoretex-ng

> A nice, java-based, MYSQL/Oracle/MSSQL/ODBC attack framework

Recipe source of truth: Pentoo [`metacoretex-ng.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/metacoretex-ng) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask metacoretex-ng
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss metacoretex-ng`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://metacoretex-ng.sourceforge.net) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-java/ant`, `virtual/jre`

Backlinks: [[scanner]] · [[metacoretex-ng]] · [[Pentoo Gap Analysis]]
