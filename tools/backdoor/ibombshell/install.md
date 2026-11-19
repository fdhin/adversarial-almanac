---
tool: "ibombshell"
build-system: "python"
upstream: "https://github.com/Telefonica/ibombshell"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/i/ibombshell"
version: "0~git20201107-0parrot2"
---

# Installing ibombshell

> Dynamic Remote Shell This package contains a tool written in Powershell that allows you to have a prompt at any time with post-exploitation functionalities (and in some cases exploitation). It is a sh

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install ibombshell
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss ibombshell`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/Telefonica/ibombshell
cd ibombshell
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `termcolor`, `pynput`
- Runtime (Debian names): `powershell`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[backdoor]] · [[ibombshell]] · [[Parrot Gap Analysis]]
