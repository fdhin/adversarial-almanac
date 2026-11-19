---
tool: "zonedb"
build-system: "go"
upstream: "https://github.com/zonedb/zonedb"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/z/zonedb"
version: "1.0.3170-0parrot2"
---

# Installing zonedb

> Public Zone Database (program) This package provides a free, open-source database (http://opendatacommons.org/licenses/odbl/1.0/) containing a list and associated metadata of public DNS zones (http://

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install zonedb
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss zonedb`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/zonedb/zonedb/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/zonedb/zonedb
cd zonedb
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Build (Debian names): `golang-any`, `golang-github-miekg-dns-dev`, `golang-github-puerkitobio-goquery-dev`, `golang-github-wsxiaoys-terminal-dev`, `golang-golang-x-net-dev`, `golang-golang-x-text-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --builddirectory=_build --buildsystem=golang --with=golang
```

Backlinks: [[recon]] · [[zonedb]] · [[Parrot Gap Analysis]]
