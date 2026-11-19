---
tool: "emailharvester"
build-system: "python"
upstream: "https://github.com/maldevel/EmailHarvester"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/e/emailharvester"
version: "1.3.2+git20191005-0parrot1"
---

# Installing emailharvester

> Email addresses harvester This package contains EmailHarvester, a tool to retrieve Domain email addresses from Search Engines. Features:  * Retrieve Domain email addresses from popular Search engines 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install emailharvester
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss emailharvester`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/maldevel/EmailHarvester
cd EmailHarvester
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `colorama`, `requests`, `termcolor`, `validators`
- Build (Debian names): `python3-all`, `python3-setuptools`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3
```

Backlinks: [[recon]] · [[emailharvester]] · [[Parrot Gap Analysis]]
