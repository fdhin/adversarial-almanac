---
tool: "absinthe"
build-system: "scripts"
upstream: "http://www.0x90.org/releases/absinthe/"
recipe: "pentoo-ebuild"
version: "1.4.1"
license: "|| ( GPL-2 BSD )"
---

# Installing absinthe

> a gui-based tool for aiding in Blind SQL Injection

Recipe source of truth: Pentoo [`absinthe.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/dev-db/absinthe) (version `1.4.1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask absinthe
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss absinthe`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.0x90.org/releases/absinthe/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-dotnet/wxnet-0.7.2`, `dev-lang/mono-1`

Backlinks: [[database]] · [[absinthe]] · [[Pentoo Gap Analysis]]
