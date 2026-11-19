---
tool: "pbounce"
build-system: "scripts"
upstream: "http://www.o0o.nu/projects/bouncers"
recipe: "pentoo-ebuild"
version: "0.1"
license: "GPL-2"
---

# Installing pbounce

> Multiplexes multiple TCP connections into internal network via reverse connection

Recipe source of truth: Pentoo [`pbounce.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-proxy/pbounce) (version `0.1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask pbounce
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss pbounce`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.o0o.nu/projects/bouncers) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[proxy]] · [[pbounce]] · [[Pentoo Gap Analysis]]
