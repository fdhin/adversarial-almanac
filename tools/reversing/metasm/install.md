---
tool: "metasm"
build-system: "ruby"
upstream: "http://metasm.cr0.org/"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-2"
---

# Installing metasm

> Metasm is a cross-architecture assembler, disassembler, compiler, linker and debugger

Recipe source of truth: Pentoo [`metasm.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/dev-util/metasm) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask metasm
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss metasm`).

## Any other Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](http://metasm.cr0.org/) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Ruby (gem): `ruby-gtk2`

Backlinks: [[reversing]] · [[metasm]] · [[Pentoo Gap Analysis]]
