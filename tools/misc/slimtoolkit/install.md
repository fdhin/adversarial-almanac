---
tool: "slimtoolkit"
build-system: "go"
upstream: "https://github.com/slimtoolkit/slim"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/s/slimtoolkit"
version: "1.40.4-0parrot1"
---

# Installing slimtoolkit

> optimization of your containers This package contains Slim(toolkit). It was called DockerSlim, and it is now just Slim (SlimToolkit). 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install slimtoolkit
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss slimtoolkit`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/slimtoolkit/slim/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/slimtoolkit/slim
cd slim
go build ./...   # exact recipe flags below
```

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --builddirectory=_build --buildsystem=golang
execute_after_dh_auto_configure:
override_dh_auto_build:
	cd cmd/slim && go build
	cd cmd/slim-sensor && go build
override_dh_auto_install:
override_dh_auto_test:
override_dh_dwz:
override_dh_installdocs:
```

Backlinks: [[misc]] · [[slimtoolkit]] · [[Parrot Gap Analysis]]
