---
tool: "bokken"
build-system: "python"
upstream: "https://inguma.eu/projects/bokken"
recipe: "pentoo-ebuild"
version: "1.8"
license: "GPL-2"
---

# Installing bokken

> GUI for the Pyew malware analysis tool and Radare the reverse engineering framework

Recipe source of truth: Pentoo [`bokken.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/dev-util/bokken) (version `1.8`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask bokken
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss bokken`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://inguma.eu/projects/bokken) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `pillow`, `psycopg`, `pygraphviz`, `pygtksourceview`
- Gentoo packages: `dev-libs/radare2-bindings`, `dev-util/radare2-0.9.6`

Backlinks: [[reversing]] · [[bokken]] · [[Pentoo Gap Analysis]]
