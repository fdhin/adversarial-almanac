---
tool: "dvwa"
build-system: "scripts"
upstream: "https://github.com/digininja/DVWA"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/d/dvwa"
version: "2.2.2-0parrot1"
---

# Installing dvwa

> Damn Vulnerable Web Application This package contains a PHP/MySQL web application that is damn vulnerable. Its main goal is to be an aid for security professionals to test their skills and tools in a 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install dvwa
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss dvwa`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/digininja/DVWA
cd DVWA
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `adduser`, `apache2`, `libapache2-mod-php`, `mariadb-server`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_link:
override_dh_compress:
```

Backlinks: [[webapp]] · [[dvwa]] · [[Parrot Gap Analysis]]
