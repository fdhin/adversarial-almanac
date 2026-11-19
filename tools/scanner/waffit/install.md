---
tool: "waffit"
build-system: "python"
upstream: "http://code.google.com/p/waffit/"
recipe: "pentoo-ebuild"
version: "9999"
license: "BSD"
---

# Installing waffit

> web application firewall (waf) scanner

Recipe source of truth: Pentoo [`waffit.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/waffit) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask waffit
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss waffit`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/waffit/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `beautifulsoup`

Backlinks: [[scanner]] · [[waffit]] · [[Pentoo Gap Analysis]]
