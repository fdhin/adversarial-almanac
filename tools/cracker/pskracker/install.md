---
tool: "pskracker"
build-system: "scripts"
upstream: "https://github.com/soxrok2212/PSKracker"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/p/pskracker"
version: "0.3.1+git20230102-0parrot1"
---

# Installing pskracker

> collection of WPA/WPA2/WPS default keys generators/pingens This package contains a collection of WPA/WPA2/WPS default algorithms/password generators/pingens written in C. This is useful for testing/au

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install pskracker
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss pskracker`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/soxrok2212/PSKracker
cd PSKracker
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `pskracker-data`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_install:
override_dh_fixperms-indep:
```

Backlinks: [[cracker]] · [[pskracker]] · [[Parrot Gap Analysis]]
