---
tool: "patchleaks"
build-system: "go"
upstream: "https://github.com/hatlesswizard/PatchLeaks"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/p/patchleaks"
version: "0.0~git20251101.8837aac-0parrot1"
---

# Installing patchleaks

> Go from a CVE number to the exact patched code and its vulnerability analysis This package contains a tool to compare two versions of a code‑base, highlight lines changed by vendor, and explain why th

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install patchleaks
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss patchleaks`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/hatlesswizard/PatchLeaks/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/hatlesswizard/PatchLeaks
cd PatchLeaks
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Runtime (Debian names): `libjs-jquery`, `node-fortawesome-fontawesome-free`
- Build (Debian names): `golang-any`, `golang-github-google-uuid-dev`, `golang-github-gorilla-mux-dev`, `golang-github-gorilla-sessions-dev`, `golang-github-smacker-go-tree-sitter-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --buildsystem=golang --with=golang --builddirectory=_build
override_dh_auto_install:
```

Backlinks: [[scanner]] · [[patchleaks]] · [[Parrot Gap Analysis]]
