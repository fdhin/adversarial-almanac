---
tool: "anonsurf"
build-system: "distro-package"
recipe: "parrot-repo"
version: "6.0.1+parrot2"
---

# Installing anonsurf

> Anonymization toolkit for Parrot Security OS

Recipe source of truth: Parrot Security's repository (Parrot Team maintained).

## Parrot / Debian-based (with Parrot repo)

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install anonsurf
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss anonsurf`); otherwise build from source.

## Any other Linux distro

Find the upstream source for **anonsurf** (search the name on GitHub) and follow its README; Parrot's package page: https://pkg.parrot.sh/package/anonsurf

## Runtime dependencies (from Parrot package)

- `iptables`, `network-manager`, `procps`, `tor`, `systemd`, `sudo`

Backlinks: [[networking]] · [[anonsurf]] · [[Parrot Gap Analysis]]
