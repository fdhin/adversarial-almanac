---
tool: "ident-user-enum"
build-system: "scripts"
upstream: "https://pentestmonkey.net/tools/user-enumeration/ident-user-enum"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/i/ident-user-enum"
version: "1.0-0parrot1"
---

# Installing ident-user-enum

> Query ident to determine the owner of a TCP network process This package is a simple PERL script to query the ident service (113/TCP) in order to determine the owner of the process listening on each T

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install ident-user-enum
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss ident-user-enum`); otherwise build from source.

## Any other Linux distro

**From source:**

**Source:** download the release from [upstream site](https://pentestmonkey.net/tools/user-enumeration/ident-user-enum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `perl`, `libnet-ident-perl`, `libio-socket-ip-perl`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_install:
```

Backlinks: [[recon]] · [[ident-user-enum]] · [[Parrot Gap Analysis]]
