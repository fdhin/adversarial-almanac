---
tool: "wordlistraider"
build-system: "python"
upstream: "https://github.com/GregorBiswanger/WordlistRaider"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/w/wordlistraider"
version: "1.0~git20200927-0parrot1"
---

# Installing wordlistraider

> Tool to prepare existing wordlists This package contains a Python tool for preparing existing wordlists. It returns a selection of words that matches the passed conditions in an existing list. As an e

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install wordlistraider
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss wordlistraider`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/GregorBiswanger/WordlistRaider
cd WordlistRaider
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Runtime (Debian names): `figlet`
- Build (Debian names): `python3-all`, `python3-setuptools`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3 --buildsystem=pybuild
override_dh_auto_install:
```

Backlinks: [[cracker]] · [[wordlistraider]] · [[Parrot Gap Analysis]]
