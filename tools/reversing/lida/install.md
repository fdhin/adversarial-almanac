---
tool: "lida"
build-system: "scripts"
upstream: "http://lida.sourceforge.net/"
recipe: "pentoo-ebuild"
version: "03.00.00"
license: "GPL-1"
---

# Installing lida

> interactive ELF disambler

Recipe source of truth: Pentoo [`lida.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/dev-util/lida) (version `03.00.00`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask lida
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss lida`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://lida.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Perl (CPAN): `perl-tk`

Backlinks: [[reversing]] · [[lida]] · [[Pentoo Gap Analysis]]
