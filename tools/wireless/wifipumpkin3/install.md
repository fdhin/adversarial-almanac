---
tool: "wifipumpkin3"
build-system: "python"
upstream: "https://github.com/P0cL4bs/wifipumpkin3"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/w/wifipumpkin3"
version: "1.1.5-0parrot1"
---

# Installing wifipumpkin3

> Powerful framework for rogue access point attack This package contains a powerful framework for rogue access point attack, written in Python, that allow and offer to security researchers, red teamers 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install wifipumpkin3
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss wifipumpkin3`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/P0cL4bs/wifipumpkin3
cd wifipumpkin3
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `distutils`
- Runtime (Debian names): `hostapd`, `iptables`, `iw`, `net-tools`, `wireless-tools`
- Build (Debian names): `python3-all`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3 --buildsystem=pybuild
```

Backlinks: [[wireless]] · [[wifipumpkin3]] · [[Parrot Gap Analysis]]
