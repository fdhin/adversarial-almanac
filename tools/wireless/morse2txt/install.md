---
tool: "morse2txt"
build-system: "make"
upstream: "http://morse2txt.sourceforge.net/"
recipe: "pentoo-ebuild"
version: "1.0.0"
license: "GPL-2"
---

# Installing morse2txt

> Morse code decoder

Recipe source of truth: Pentoo [`morse2txt.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/morse2txt) (version `1.0.0`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask morse2txt
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss morse2txt`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://morse2txt.sourceforge.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/atk`, `dev-libs/glib`, `gnome-base/libgnome`, `gnome-base/libgnomeui`, `sci-libs/gsl`, `x11-libs/gdk-pixbuf`, `x11-libs/gtk+`

Backlinks: [[wireless]] · [[morse2txt]] · [[Pentoo Gap Analysis]]
