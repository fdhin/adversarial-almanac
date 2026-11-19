---
tool: "woeusb-ng"
build-system: "python"
upstream: "https://github.com/WoeUSB/WoeUSB-ng"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/w/woeusb-ng"
version: "0.2.12-1parrot2"
---

# Installing woeusb-ng

> Create Windows Installer on Linux A simple tool that enable you to create your own usb stick windows installer from an iso image or a real DVD. This is a rewrite of original WoeUSB.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install woeusb-ng
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss woeusb-ng`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/WoeUSB/WoeUSB-ng
cd WoeUSB-ng
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Runtime (Debian names): `grub2-common`, `grub-pc-bin`, `parted`, `dosfstools`, `ntfs-3g`
- Build (Debian names): `python3-setuptools`, `python3-all`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3 --buildsystem=pybuild
override_dh_install:
	install ./WoeUSB/woeusbgui debian/woeusb-ng/usr/bin/woeusbgui
	install ./WoeUSB/data/icon.ico debian/woeusb-ng/usr/share/woeusb-ng/icon.ico
```

Backlinks: [[windows]] · [[woeusb-ng]] · [[Parrot Gap Analysis]]
