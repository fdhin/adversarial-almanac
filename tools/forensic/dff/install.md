---
tool: "dff"
build-system: "python"
upstream: "http://tracker.digital-forensic.org/"
recipe: "pentoo-ebuild"
version: "1.3.0"
license: "GPL-2"
---

# Installing dff

> A digital forensics framework which aims to analyze and recover any
kind of digital artifact.

Recipe source of truth: Pentoo [`dff.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-forensics/dff) (version `1.3.0`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask dff
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss dff`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://tracker.digital-forensic.org/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `pyqt4-4.4.0`, `sip`
- Gentoo packages: `app-forensics/afflib-3.6.8`, `app-forensics/libbfio-0.0.20120425`, `app-forensics/libewf-20100226`, `app-forensics/libpff-0.0.20120802_alpha`, `dev-lang/swig-1.3.38`, `sys-apps/file-4.26`

Backlinks: [[forensic]] · [[dff]] · [[Pentoo Gap Analysis]]
