---
tool: "streams"
build-system: "scripts"
upstream: "http://src.carnivore.it/streams/about"
recipe: "pentoo-ebuild"
version: "0.1.0"
license: "GPL-2"
---

# Installing streams

> streams is a tool for browsing, mining and processing TCP streams in pcap files

Recipe source of truth: Pentoo [`streams.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-forensics/streams) (version `0.1.0`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask streams
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss streams`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://src.carnivore.it/streams/about) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `net-libs/libpcap`, `sys-libs/readline`

Backlinks: [[forensic]] · [[streams]] · [[Pentoo Gap Analysis]]
