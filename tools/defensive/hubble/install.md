---
tool: "hubble"
build-system: "go"
upstream: "https://github.com/cilium/hubble"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/h/hubble"
version: "1.18.5-0parrot1"
---

# Installing hubble

> Network, Service & Security Observability for Kubernetes using eBPF (program) Hubble is a fully distributed networking and security observability platform for cloud native workloads. It is built on to

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install hubble
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss hubble`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/cilium/hubble/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/cilium/hubble
cd hubble
go build ./...   # exact recipe flags below
```

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --builddirectory=_build --buildsystem=golang --with=bash-completion
override_dh_auto_install:
override_dh_dwz:
override_dh_installdocs:
```

Backlinks: [[defensive]] · [[hubble]] · [[Parrot Gap Analysis]]
