---
tool: "protos"
build-system: "scripts"
upstream: "http://www.ee.oulu.fi/research/ouspg/protos/"
recipe: "pentoo-ebuild"
version: "1.1"
license: "GPL-2"
---

# Installing protos

> The protos fuzzing collection, containing http-reply, ldapv3, snmpv1, sip, h2250v4 and isakmp

Recipe source of truth: Pentoo [`protos.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-fuzz/protos) (version `1.1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask protos
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss protos`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.ee.oulu.fi/research/ouspg/protos/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `virtual/jre`

Backlinks: [[fuzzer]] · [[protos]] · [[Pentoo Gap Analysis]]
