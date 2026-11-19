---
tool: "dnscat2"
build-system: "scripts"
upstream: "https://github.com/iagox86/dnscat2"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/d/dnscat2"
version: "0.07-0parrot1"
---

# Installing dnscat2

> DNS tunnel (metapackage) This tool is designed to create an encrypted command-and-control (C&C) channel over the DNS protocol, which is an effective tunnel out of almost every network.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install dnscat2
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss dnscat2`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/iagox86/dnscat2
cd dnscat2
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `dnscat2-client`, `dnscat2-server`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_fixperms-indep:
```

Backlinks: [[tunnel]] · [[dnscat2]] · [[Parrot Gap Analysis]]
