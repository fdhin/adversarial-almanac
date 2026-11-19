---
tool: "dufflebag"
build-system: "scripts"
upstream: "https://github.com/BishopFox/dufflebag"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/d/dufflebag"
version: "0.0~git20200205.9a01942-0parrot1"
---

# Installing dufflebag

> Search exposed EBS volumes for secrets (program) Dufflebag is a tool that searches through public Elastic Block Storage (EBS) snapshots for secrets that may have been accidentally left in.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install dufflebag
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss dufflebag`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/BishopFox/dufflebag
cd dufflebag
# run scripts directly (see recipe for interpreter and layout)
```

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_build:
```

Backlinks: [[recon]] · [[dufflebag]] · [[Parrot Gap Analysis]]
