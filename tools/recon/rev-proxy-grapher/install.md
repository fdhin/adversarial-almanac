---
tool: "rev-proxy-grapher"
build-system: "python"
upstream: "https://github.com/mricon/rev-proxy-grapher"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/r/rev-proxy-grapher"
version: "0~git20180301-0parrot1"
---

# Installing rev-proxy-grapher

> Reverse proxy grapher This package contains a useful little tool that will generate a nice graphviz graph illustrating your reverse proxy flow. It takes a manually curated YAML file describing the top

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install rev-proxy-grapher
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss rev-proxy-grapher`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/mricon/rev-proxy-grapher
cd rev-proxy-grapher
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `netaddr`, `nmap`, `pydotplus`, `yaml`
- Build (Debian names): `python3-all`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3
```

Backlinks: [[recon]] · [[rev-proxy-grapher]] · [[Parrot Gap Analysis]]
