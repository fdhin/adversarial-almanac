---
tool: "seclists-lite"
build-system: "scripts"
upstream: "https://www.owasp.org/index.php/Projects/OWASP_SecLists_Project"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/s/seclists-lite"
version: "2024.4-0parrot1"
---

# Installing seclists-lite

> Minimal collection of multiple types of security lists seclists-lite is a collection of multiple types of lists used during security assessments. List types included are a small subset of the larger S

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install seclists-lite
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss seclists-lite`); otherwise build from source.

## Any other Linux distro

**From source:**

**Source:** download the release from [upstream site](https://www.owasp.org/index.php/Projects/OWASP_SecLists_Project) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
# -*- makefile -*-
# As a special exception, when this file is copied by dh-make into a
# dh-make output file, you may use that output file without restriction.
# This special exception was added by Craig Small in version 0.37 of dh-make.
	dh $@
```

Backlinks: [[wordlist]] · [[seclists-lite]] · [[Parrot Gap Analysis]]
