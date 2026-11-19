---
tool: "humble"
build-system: "python"
upstream: "https://github.com/rfc-st/humble"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/h/humble"
version: "1.28-0parrot1"
---

# Installing humble

> HTTP Headers Analyzer This package contains an  humble, and fast, security-oriented HTTP headers analyzer.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install humble
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss humble`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/rfc-st/humble
cd humble
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `colorama`, `fpdf`, `requests`, `tldextract`
- Runtime (Debian names): `publicsuffix`
- Build (Debian names): `python3-setuptools`, `python3-all`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ #--buildsystem=pybuild
override_dh_install:
```

Backlinks: [[recon]] · [[humble]] · [[Parrot Gap Analysis]]
