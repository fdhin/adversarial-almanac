---
tool: "sigma-cli"
build-system: "python"
upstream: "https://github.com/SigmaHQ/sigma-cli"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/s/sigma-cli"
version: "0.7.7-0parrot1"
---

# Installing sigma-cli

> Sigma command line interface This package contains the Sigma command line interface using the pySigma library to manage, list and convert Sigma rules into query languages.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install sigma-cli
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss sigma-cli`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/SigmaHQ/sigma-cli
cd sigma-cli
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `sigma`
- Build (Debian names): `pybuild-plugin-pyproject`, `python3-all`, `python3-poetry-core`, `python3-click`, `python3-prettytable`, `python3-pytest`, `python3-sigma`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --buildsystem=pybuild
```

Backlinks: [[defensive]] · [[sigma-cli]] · [[Parrot Gap Analysis]]
