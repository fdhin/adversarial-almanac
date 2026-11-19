---
tool: "evilginx2"
build-system: "go"
upstream: "https://github.com/kgretzky/evilginx2"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/e/evilginx2"
version: "3.3.0+ds1-0parrot2"
---

# Installing evilginx2

> man-in-the-middle attack framework This package contains a man-in-the-middle attack framework used for phishing login credentials along with session cookies, which in turn allows to bypass 2-factor au

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install evilginx2
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss evilginx2`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/kgretzky/evilginx2/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/kgretzky/evilginx2
cd evilginx2
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Build (Debian names): `golang-any`, `golang-github-elazarl-goproxy-dev`, `golang-github-fsnotify-fsnotify-dev`, `golang-github-gorilla-mux-dev`, `golang-vhost-dev`, `golang-github-mattn-go-colorable-dev`, `golang-github-mattn-go-isatty-dev`, `golang-github-mitchellh-mapstructure-dev`, `golang-github-pelletier-go-toml-dev`, `golang-github-tidwall-btree-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --builddirectory=_build --buildsystem=golang --with=golang
override_dh_auto_install:
execute_after_dh_auto_configure:
```

Backlinks: [[spoof]] · [[evilginx2]] · [[Parrot Gap Analysis]]
