---
tool: "copy-router-config"
build-system: "scripts"
upstream: "https://www.offsec.com"
recipe: "kali-debian"
version: "1.0-1kali5"
---

# Installing copy-router-config

> Copies Cisco configs via SNMP This package copies configuration files from Cisco devices running SNMP.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/copy-router-config) (version `1.0-1kali5`).

## Kali / Debian-based

```bash
sudo apt install copy-router-config
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss copy-router-config`); otherwise build from source below.

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.offsec.com) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `perl-cisco-copyconfig`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[scanner]] · [[copy-router-config]] · [[Kali Gap Analysis]]
