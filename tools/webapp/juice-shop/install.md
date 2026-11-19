---
tool: "juice-shop"
build-system: "scripts"
upstream: "https://github.com/juice-shop/juice-shop"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/j/juice-shop"
version: "14.5.1+node18-0parrot1"
---

# Installing juice-shop

> insecure web application This package contains a modern and sophisticated insecure web application! It can be used in security trainings, awareness demos, CTFs and as a guinea pig for security tools! 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install juice-shop
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss juice-shop`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/juice-shop/juice-shop
cd juice-shop
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `npm`, `lsof`
- Build (Debian names): `nodejs`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_installsystemd:
override_dh_gencontrol:
```

Backlinks: [[webapp]] · [[juice-shop]] · [[Parrot Gap Analysis]]
