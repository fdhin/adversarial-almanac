---
tool: "linen"
build-system: "scripts"
upstream: "http://www.encaseenterprise.com/support/LinEn_LicenseAgreement.aspx"
recipe: "pentoo-ebuild"
version: "61"
license: "as-is"
---

# Installing linen

> Encase's linen utility

Recipe source of truth: Pentoo [`linen.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-forensics/linen) (version `61`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask linen
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss linen`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.encaseenterprise.com/support/LinEn_LicenseAgreement.aspx) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `app-arch/unzip`

Backlinks: [[forensic]] · [[linen]] · [[Pentoo Gap Analysis]]
