---
tool: "nsia"
ebuild-package: "nsia"
build-system: "scripts"
upstream: "http://threatfactor.com/Products/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/nsia"
version: "1.0.6"
license: "custom:${pkgname}"
---

# Installing nsia

> A website scanner that monitors websites in realtime in order to detect defacements, compliance violations, exploits, sensitive information disclosure and other issues.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/nsia) (version `1.0.6`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S nsia
```

The same PKGBUILD is usually on the AUR (`paru -S nsia`).

## Any other Linux distro

**From source:**

```bash
curl -fLO http://threatfactor.com/Support/Files/ThreatFactor%20NSIA.zip
tar xf ThreatFactor%20NSIA.zip
cd ThreatFactor%20NSIA
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `hd2u`
  - `swt`
  - `systemd`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[scanner]] · [[nsia]] · [[ArchStrike Gap Analysis]]
