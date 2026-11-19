---
tool: "sshttp"
build-system: "scripts"
upstream: "http://c-skills.blogspot.com/"
recipe: "pentoo-ebuild"
version: "0.25"
license: "BSD"
---

# Installing sshttp

> an easy to use OSI-Layer5 switching daemon

Recipe source of truth: Pentoo [`sshttp.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-proxy/sshttp) (version `0.25`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask sshttp
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss sshttp`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://c-skills.blogspot.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `net-libs/libpcap`

Backlinks: [[proxy]] · [[sshttp]] · [[Pentoo Gap Analysis]]
