---
tool: "fuzz"
build-system: "scripts"
upstream: "http://taviso.decsystem.org/software.html#toc2"
recipe: "pentoo-ebuild"
version: "0.4"
license: "GPL-2"
---

# Installing fuzz

> Python based fuzzer for multi protocols, and faultinject

Recipe source of truth: Pentoo [`fuzz.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-fuzz/fuzz) (version `0.4`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask fuzz
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss fuzz`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://taviso.decsystem.org/software.html#toc2) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[fuzzer]] · [[fuzz]] · [[Pentoo Gap Analysis]]
