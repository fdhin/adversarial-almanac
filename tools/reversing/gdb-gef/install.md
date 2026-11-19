---
tool: "gdb-gef"
build-system: "python"
upstream: "https://gef.readthedocs.io/en/master/"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/g/gdb-gef"
version: "2026.01-0parrot1"
---

# Installing gdb-gef

> GDB Enchanced Features for exploit develpoment GEF (pronounced ʤɛf - "Jeff") is a set of commands for x86/64, ARM, MIPS, PowerPC and SPARC to assist exploit developers and reverse-engineers when using

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install gdb-gef
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss gdb-gef`); otherwise build from source.

## Any other Linux distro

**From source:**

**Source:** download the release from [upstream site](https://gef.readthedocs.io/en/master/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Runtime (Debian names): `gdb`, `python3`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_install:
override_dh_auto_configure:
override_dh_auto_build:
override_dh_auto_test:
```

Backlinks: [[reversing]] · [[gdb-gef]] · [[Parrot Gap Analysis]]
