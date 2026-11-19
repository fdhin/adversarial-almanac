---
tool: "mcpwn"
build-system: "go"
upstream: "https://gitlab.com/parrotsec/project/mcpwn"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/m/mcpwn"
version: "1.3.0-0parrot2"
---

# Installing mcpwn

> MCP server that allows LLMs to execute security tools MCPwn is a Model Context Protocol (MCP) server that provides a bridge between Large Language Models and local security tools, allowing automated e

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install mcpwn
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss mcpwn`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://gitlab.com/parrotsec/project/mcpwn/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://gitlab.com/parrotsec/project/mcpwn
cd mcpwn
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Runtime (Debian names): `podman`
- Build (Debian names): `golang-any`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --buildsystem=golang --with=golang
override_dh_auto_test:
```

Backlinks: [[misc]] · [[mcpwn]] · [[Parrot Gap Analysis]]
