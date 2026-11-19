---
tool: "cyberchef"
build-system: "scripts"
upstream: "https://github.com/gchq/CyberChef"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/c/cyberchef"
version: "10.4.0-0parrot1"
---

# Installing cyberchef

> Cyber Swiss Army Knife This package contains a simple, intuitive web app for carrying out all manner of "cyber" operations within a web browser. These operations include simple encoding like XOR and B

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install cyberchef
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss cyberchef`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/gchq/CyberChef
cd CyberChef
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Build (Debian names): `npm`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_install:
override_dh_install:
```

Backlinks: [[misc]] · [[cyberchef]] · [[Parrot Gap Analysis]]
