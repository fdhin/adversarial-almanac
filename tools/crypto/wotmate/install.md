---
tool: "wotmate"
build-system: "python"
upstream: "https://github.com/mricon/wotmate"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/w/wotmate"
version: "0.1+git20210512-0parrot1"
---

# Installing wotmate

> reimplement the defunct PGP pathfinder with only your own keyring This package contains a reimplementation the defunct PGP pathfinder without needing anything other than your own keyring.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install wotmate
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss wotmate`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/mricon/wotmate
cd wotmate
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `pydotplus`
- Build (Debian names): `python3-setuptools`, `python3-all`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3
```

Backlinks: [[crypto]] · [[wotmate]] · [[Parrot Gap Analysis]]
