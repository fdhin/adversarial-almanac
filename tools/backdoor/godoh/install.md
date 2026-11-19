---
tool: "godoh"
build-system: "go"
upstream: "https://github.com/sensepost/goDoH"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/g/godoh"
version: "1.6+git20200517-0parrot1"
---

# Installing godoh

> DNS-over-HTTPS Command & Control Proof of Concept This package contains a proof of concept Command and Control framework, written in Golang, that uses DNS-over-HTTPS as a transport medium. Currently s

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install godoh
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss godoh`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/sensepost/goDoH/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/sensepost/goDoH
cd goDoH
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Build (Debian names): `golang`, `golang-github-konsorten-go-windows-terminal-sequences-dev`, `golang-github-miekg-dns-dev`, `golang-github-sirupsen-logrus-dev`, `golang-github-spf13-cobra-dev`, `golang-golang-x-crypto-dev`, `golang-golang-x-net-dev`, `golang-golang-x-sync-dev`, `golang-golang-x-sys-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --buildsystem=golang --with=golang
override_dh_auto_install:
```

Backlinks: [[backdoor]] · [[godoh]] · [[Parrot Gap Analysis]]
