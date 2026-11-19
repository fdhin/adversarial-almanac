---
tool: "upnpwn"
build-system: "scripts"
upstream: "http://bigbrainlabs.blogspot.com/"
recipe: "pentoo-ebuild"
version: "0.8.0-r1"
license: "GPL-2"
---

# Installing upnpwn

> UPNP pentesting tool and library

Recipe source of truth: Pentoo [`upnpwn.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/upnpwn) (version `0.8.0-r1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask upnpwn
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss upnpwn`).

## Any other Linux distro

**From source:**

```bash
curl -fLO http://dev.pentoo.ch/~jensp/distfiles/UPnPwn-0.8.0-bh.tgz
tar xf UPnPwn-0.8.0-bh.tgz
cd UPnPwn-0.8.0-bh
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-lang/ruby-1.8`

Backlinks: [[scanner]] · [[upnpwn]] · [[Pentoo Gap Analysis]]
