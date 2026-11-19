---
tool: "colly"
build-system: "go"
upstream: "https://github.com/gocolly/colly"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/c/colly"
version: "2.1.0-0parrot1"
---

# Installing colly

> Elegant Scraper and Crawler Framework for Golang (program) This package contains a Colly Lightning Fast and Elegant Scraping Framework for Gophers.  Colly provides a clean interface to write any kind 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install colly
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss colly`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/gocolly/colly/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/gocolly/colly
cd colly
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Build (Debian names): `golang-any`, `golang-github-antchfx-htmlquery-dev`, `golang-github-antchfx-xmlquery-dev`, `golang-github-gobwas-glob-dev`, `golang-github-jawher-mow.cli-dev`, `golang-github-kennygrant-sanitize-dev`, `golang-github-nlnwa-whatwg-url-dev`, `golang-github-puerkitobio-goquery-dev`, `golang-github-saintfish-chardet-dev`, `golang-golang-x-net-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --builddirectory=_build --buildsystem=golang --with=golang
```

Backlinks: [[recon]] · [[colly]] · [[Parrot Gap Analysis]]
