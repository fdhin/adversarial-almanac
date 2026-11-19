---
tool: "encryptpad"
build-system: "python"
upstream: "https://github.com/evpo/EncryptPad"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/e/encryptpad"
version: "0.5.0.3-1parrot1"
---

# Installing encryptpad

> Text editor for password protecting and encrypting files Minimalist secure text editor and binary encryptor that  implements RFC 4880 Open PGP format: symmetrically  encrypted, compressed and integrit

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install encryptpad
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss encryptpad`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/evpo/EncryptPad
cd EncryptPad
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Build (Debian names): `python3`, `pkg-config`, `libbotan-2-dev`, `zlib1g-dev`, `qtbase5-dev`, `gcc`, `g++`, `make`, `libbz2-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --
override_dh_auto_configure:
	python3 configure.py
override_dh_auto_install:
# dh_make generated override targets
# This is example for Cmake (See https://bugs.debian.org/641051 )
#override_dh_auto_configure:
#	dh_auto_configure -- #	-DCMAKE_LIBRARY_PATH=$(DEB_HOST_MULTIARCH)
```

Backlinks: [[crypto]] · [[encryptpad]] · [[Parrot Gap Analysis]]
