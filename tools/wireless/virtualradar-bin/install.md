---
tool: "virtualradar-bin"
build-system: "prebuilt"
upstream: "http://www.virtualradarserver.co.uk/Default.aspx"
recipe: "pentoo-ebuild"
version: "2.0.1"
license: "unknown"
---

# Installing virtualradar-bin

> Open-source .NET application for ads-b mapping

Recipe source of truth: Pentoo [`virtualradar-bin.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/virtualradar-bin) (version `2.0.1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask virtualradar-bin
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss virtualradar-bin`).

## Any other Linux distro


## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-lang/mono`

Backlinks: [[wireless]] · [[virtualradar-bin]] · [[Pentoo Gap Analysis]]
