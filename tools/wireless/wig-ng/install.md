---
tool: "wig-ng"
build-system: "python"
upstream: "https://github.com/6e726d/wig-ng"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/w/wig-ng"
version: "0.1-0parrot1"
---

# Installing wig-ng

> utility for Wi-Fi device fingerprinting This package contains WIG (Wi-Fi Information Gathering), a utility for Wi-Fi device fingerprinting. Supported protocols and standards:   * Apple Wireless Direct

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install wig-ng
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss wig-ng`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/6e726d/wig-ng
cd wig-ng
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `impacket`, `pcapy`, `setproctitle`
- Build (Debian names): `python3-all`, `python3-setuptools`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3 --buildsystem=pybuild
```

Backlinks: [[wireless]] · [[wig-ng]] · [[Parrot Gap Analysis]]
