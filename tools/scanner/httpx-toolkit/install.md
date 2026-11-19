---
tool: "httpx-toolkit"
build-system: "go"
upstream: "https://github.com/projectdiscovery/httpx"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/h/httpx-toolkit"
version: "1.7.4-0parrot2"
---

# Installing httpx-toolkit

> fast and multi-purpose HTTP toolkit This package contains the httpX toolkit developed by ProjectDiscovery. It's a fast and multi-purpose HTTP toolkit allow to run multiple probers using retryablehttp 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install httpx-toolkit
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss httpx-toolkit`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/httpx/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/projectdiscovery/httpx
cd httpx
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Build (Debian names): `ca-certificates`, `golang-any`, `golang-github-corpix-uarand-dev`, `golang-github-julienschmidt-httprouter-dev`, `golang-github-logrusorgru-aurora-dev`, `golang-github-microcosm-cc-bluemonday-dev`, `golang-github-pkg-errors-dev`, `golang-github-remeh-sizedwaitgroup-dev`, `golang-github-rs-xid-dev`, `golang-golang-x-net-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --builddirectory=_build --buildsystem=golang
override_dh_auto_build:
	make
override_dh_auto_install:
override_dh_dwz:
override_dh_auto_test:
```

Backlinks: [[scanner]] · [[httpx-toolkit]] · [[Parrot Gap Analysis]]
