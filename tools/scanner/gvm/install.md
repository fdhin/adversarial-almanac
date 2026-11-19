---
tool: "gvm"
build-system: "scripts"
upstream: "http://www.kali.org"
recipe: "kali-debian"
aur-package: "gvm"
version: "25.04.3"
---

# Installing gvm

> dummy package for openvas Dummy package for the opvas vulnerability scanner.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/gvm) (version `25.04.3`).

## Kali / Debian-based

```bash
sudo apt install gvm
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss gvm`); otherwise build from source below.

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.kali.org) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `greenbone-security-assistant`, `openvas-cli`, `openvas-manager`, `openvas-scanner`, `sqlite3`, `xsltproc`, `rsync`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ 
```

Backlinks: [[scanner]] · [[gvm]] · [[Kali Gap Analysis]]
