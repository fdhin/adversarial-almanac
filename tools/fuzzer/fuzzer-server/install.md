---
tool: "fuzzer-server"
build-system: "scripts"
upstream: "http://www.atstake.com/"
recipe: "pentoo-ebuild"
version: "0.1"
license: "GPL-2"
---

# Installing fuzzer-server

> A html/wml fuzzer for proxy or client

Recipe source of truth: Pentoo [`fuzzer-server.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-fuzz/fuzzer-server) (version `0.1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask fuzzer-server
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss fuzzer-server`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.atstake.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[fuzzer]] · [[fuzzer-server]] · [[Pentoo Gap Analysis]]
