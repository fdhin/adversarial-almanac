---
tool: "op25"
build-system: "python"
upstream: "http://op25.osmocom.org/wiki"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL"
---

# Installing op25

> software-defined analyzer for APCO P25 signals

Recipe source of truth: Pentoo [`op25.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/op25) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask op25
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss op25`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://op25.osmocom.org/wiki) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/boost`, `net-libs/libpcap`, `net-wireless/gnuradio-3.7`, `sci-libs/itpp`

Backlinks: [[wireless]] · [[op25]] · [[Pentoo Gap Analysis]]
