---
tool: "origami-pdf"
build-system: "ruby"
upstream: "https://code.google.com/p/origami-pdf/"
recipe: "pentoo-ebuild"
version: "1.2.6"
license: "GPL-3"
---

# Installing origami-pdf

> A Ruby framework designed to parse, analyze, and forge PDF documents

Recipe source of truth: Pentoo [`origami-pdf.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-forensics/origami-pdf) (version `1.2.6`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask origami-pdf
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss origami-pdf`).

## Any other Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://code.google.com/p/origami-pdf/) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Ruby (gem): `ruby-gtk2`, `rubygems`
- Gentoo packages: `dev-lang/ruby`

Backlinks: [[forensic]] · [[origami-pdf]] · [[Pentoo Gap Analysis]]
