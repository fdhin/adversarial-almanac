---
tool: "maltego-teeth"
build-system: "python"
upstream: "https://www.maltego.com"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/m/maltego-teeth"
version: "1.0+20201218-0parrot2"
---

# Installing maltego-teeth

> Set of offensive Maltego transforms A set of transforms for Maltego to run nmap, sqlmap, and more against entitites in Maltego.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install maltego-teeth
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss maltego-teeth`); otherwise build from source.

## Any other Linux distro

**From source:**

**Source:** download the release from [upstream site](https://www.maltego.com) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `adns`, `bs4`, `easygui`, `levenshtein`, `mechanize`, `metaconfig`, `msgpack`
- Runtime (Debian names): `maltego`, `metasploit-framework`, `nmap`, `python3`, `sqlmap`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
# -*- makefile -*-
# As a special exception, when this file is copied by dh-make into a
# dh-make output file, you may use that output file without restriction.
# This special exception was added by Craig Small in version 0.37 of dh-make.
	dh $@ 
```

Backlinks: [[recon]] · [[maltego-teeth]] · [[Parrot Gap Analysis]]
