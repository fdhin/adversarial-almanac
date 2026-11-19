---
tool: "karma"
build-system: "scripts"
upstream: "http://www.theta44.org/karma"
recipe: "pentoo-ebuild"
nix-package: "karma"
version: "20060124"
license: "GPL-2"
---

# Installing karma

> MITM framework for wifi

Recipe source of truth: Pentoo [`karma.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-misc/karma) (version `20060124`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask karma
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss karma`).

## Nix (works on any distro)

```bash
nix run nixpkgs#karma    # ad-hoc run, no install
nix profile install nixpkgs#karma   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.theta44.org/karma) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-lang/ruby`, `net-libs/libpcap`, `net-misc/dhcp`, `net-wireless/wireless-tools`

Backlinks: [[misc]] · [[karma]] · [[Pentoo Gap Analysis]]
