---
tool: "spray"
build-system: "scripts"
upstream: "https://github.com/Greenwolf/Spray"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/s/spray"
version: "2.1+git20190226-0parrot1"
---

# Installing spray

> Password Spraying tool for Active Directory Credentials This package contains a Password Spraying tool for Active Directory Credentials. The script will password spray a target over a period of time. 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install spray
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss spray`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/Greenwolf/Spray
cd Spray
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `curl`, `smbclient`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_install:
```

Backlinks: [[cracker]] · [[spray]] · [[Parrot Gap Analysis]]
