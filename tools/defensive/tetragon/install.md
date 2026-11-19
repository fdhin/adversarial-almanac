---
tool: "tetragon"
build-system: "go"
upstream: "https://github.com/cilium/tetragon"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/t/tetragon"
version: "0.10.0-0parrot1"
---

# Installing tetragon

> eBPF-based Security Observability and Runtime Enforcement (tetra CLI) Cilium’s new Tetragon component enables powerful realtime, eBPF-based Security Observability and Runtime Enforcement.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install tetragon
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss tetragon`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/cilium/tetragon/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/cilium/tetragon
cd tetragon
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Runtime (Debian names): `bpftool`
- Build (Debian names): `bash-completion`, `ca-certificates`, `clang`, `bpftool`, `llvm`, `docker.io`, `golang-any`, `golang-google-grpc-dev`, `golang-google-protobuf-dev`, `golang-github-sirupsen-logrus-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --builddirectory=_build --buildsystem=golang --with=bash-completion
override_dh_auto_build:
	go build ./cmd/tetra/
override_dh_auto_install:
override_dh_installdocs:
override_dh_auto_test:
override_dh_dwz:
```

Backlinks: [[defensive]] · [[tetragon]] · [[Parrot Gap Analysis]]
