---
tool: "dscan"
build-system: "python"
upstream: "https://github.com/0x4E0x650x6F/dscan"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/d/dscan"
version: "0.1.5-0parrot1"
---

# Installing dscan

> wrapper around nmap This package provides a wrapper around nmap, and distribute scans across several hosts. It aggregates / splits address ranges, uses a configuration file where scan configuration ca

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install dscan
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss dscan`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/0x4E0x650x6F/dscan
cd dscan
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Build (Debian names): `python3-setuptools`, `python3-all`, `python3-sphinx`, `python3-libnmap`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3,sphinxdoc --buildsystem=pybuild
override_dh_auto_build: export http_proxy=127.0.0.1:9
override_dh_auto_build: export https_proxy=127.0.0.1:9
override_dh_auto_build:
	PYTHONPATH=. python3 -m sphinx -N -bhtml docs/ build/html
```

Backlinks: [[scanner]] · [[dscan]] · [[Parrot Gap Analysis]]
