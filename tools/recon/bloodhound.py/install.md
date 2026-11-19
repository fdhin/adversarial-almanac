---
tool: "bloodhound.py"
build-system: "python"
upstream: "https://github.com/dirkjanm/bloodhound.py"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/b/bloodhound.py"
version: "1.9.0-0parrot1"
---

# Installing bloodhound.py

> ingestor for BloodHound, based on Impacket (Python 3) This package contains a Python based ingestor for BloodHound, based on Impacket. BloodHound.py currently has the following limitations:    * Suppo

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install bloodhound.py
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss bloodhound.py`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/dirkjanm/bloodhound.py
cd bloodhound.py
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Build (Debian names): `python3-setuptools`, `python3-all`, `python3-pycryptodome`, `python3-dnspython`, `python3-impacket`, `python3-ldap3`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --buildsystem=pybuild
```

Backlinks: [[recon]] · [[bloodhound.py]] · [[Parrot Gap Analysis]]
