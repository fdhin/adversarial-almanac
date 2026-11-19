---
tool: "starkiller"
build-system: "scripts"
upstream: "https://github.com/BC-SECURITY/Starkiller"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/s/starkiller"
version: "3.0.0-rc1+ds-0parrot1"
---

# Installing starkiller

> Frontend for Powershell Empire This package contains a Frontend for Powershell Empire.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install starkiller
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss starkiller`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/BC-SECURITY/Starkiller
cd Starkiller
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Build (Debian names): `graphicsmagick-imagemagick-compat`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_build:
override_dh_shlibdeps:
```

Backlinks: [[backdoor]] · [[starkiller]] · [[Parrot Gap Analysis]]
