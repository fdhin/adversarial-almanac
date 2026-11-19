---
tool: "osrframework"
build-system: "python"
upstream: "https://pypi.org/project/osrframework/"
recipe: "kali-debian"
version: "0.20.5-0kali3"
---

# Installing osrframework

> Open Sources Research Framework This package contains a set of libraries developed by i3visio to perform Open Source Intelligence tasks. They include references to a bunch of different applications related to username checking, DNS lookups, information leaks research, deep web search, regular expressions extraction and many others.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/osrframework) (version `0.20.5-0kali3`).

## Kali / Debian-based

```bash
sudo apt install osrframework
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss osrframework`); otherwise build from source below.

## Any other Linux distro

**B. From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/osrframework/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `duckpy`
- Build (Debian names): `python3-all`, `python3-setuptools`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3 --buildsystem=pybuild
override_dh_installdocs:
```

Backlinks: [[recon]] · [[osrframework]] · [[Kali Gap Analysis]]
