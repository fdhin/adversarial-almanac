---
tool: "rocket"
build-system: "distro-package"
recipe: "parrot-repo"
version: "2.0.0+parrot1"
---

# Installing rocket

> Launcher for Container-based security tools

Recipe source of truth: Parrot Security's repository (Parrot Team maintained).

## Parrot / Debian-based (with Parrot repo)

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install rocket
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss rocket`); otherwise build from source.

## Any other Linux distro

Find the upstream source for **rocket** (search the name on GitHub) and follow its README; Parrot's package page: https://pkg.parrot.sh/package/rocket

## Runtime dependencies (from Parrot package)

- `zlib1g`, `podman`

Backlinks: [[misc]] · [[rocket]] · [[Parrot Gap Analysis]]
