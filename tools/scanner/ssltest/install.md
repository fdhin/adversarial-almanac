---
tool: "ssltest"
build-system: "scripts"
upstream: "http://sites.google.com/site/lupingreycorner/"
recipe: "pentoo-ebuild"
version: "0.4"
license: "AS IS"
---

# Installing ssltest

> SSL testing tool written in perl

Recipe source of truth: Pentoo [`ssltest.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/ssltest) (version `0.4`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask ssltest
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss ssltest`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sites.google.com/site/lupingreycorner/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Perl (CPAN): `IO-Socket-SSL`

Backlinks: [[scanner]] · [[ssltest]] · [[Pentoo Gap Analysis]]
