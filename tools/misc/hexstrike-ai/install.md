---
tool: "hexstrike-ai"
build-system: "python"
upstream: "https://github.com/0x4m4/hexstrike-ai"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/h/hexstrike-ai"
version: "0.0~git20250919.3326704-0parrot1"
---

# Installing hexstrike-ai

> AI-Powered MCP Cybersecurity Automation Platform This package contains an HexStrike AI MCP v6.0 which features a multi-agent architecture with autonomous AI agents, intelligent decision-making, and vu

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install hexstrike-ai
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss hexstrike-ai`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/0x4m4/hexstrike-ai
cd hexstrike-ai
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `aiohttp`, `bs4`, `flask`, `mcp`, `psutil`, `pwntools`, `requests`, `selenium`
- Runtime (Debian names): `mitmproxy`
- Build (Debian names): `python3-all`, `python3-setuptools`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ #--buildsystem=pybuild
```

Backlinks: [[misc]] · [[hexstrike-ai]] · [[Parrot Gap Analysis]]
