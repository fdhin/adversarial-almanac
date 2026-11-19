---
tool: "hostsman"
build-system: "python"
upstream: "https://github.com/qszhuan/hostsman"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/h/hostsman"
version: "1.1.5-0parrot1"
---

# Installing hostsman

> cross-platform command line tool for handling hosts files cross-platform command line tool for adding, removing or listing mappings in hosts file.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install hostsman
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss hostsman`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/qszhuan/hostsman
cd hostsman
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `pygments`, `nose`, `mock`, `colorama`
- Build (Debian names): `python3-all`, `python3-setuptools`, `python3-pygments`, `python3-nose`, `python3-mock`, `python3-colorama`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3 --buildsystem=pybuild
```

Backlinks: [[networking]] · [[hostsman]] · [[Parrot Gap Analysis]]
