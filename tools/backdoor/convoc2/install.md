---
tool: "convoc2"
build-system: "go"
upstream: ""
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/c/convoc2"
version: "0.1.0-0parrot7"
---

# Installing convoc2

> Command and Control infrastracture through Microsoft Teams convoC2 is a Command and Control infrastructure that allows Red Teamers to execute system commands on compromised hosts through Microsoft Tea

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install convoc2
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss convoc2`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the upstream project (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Build (Debian names): `golang-go`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_configure:
	go mod vendor
override_dh_auto_build:
	go build -mod=vendor -o build/server/convoc2_server_$(DEB_HOST_ARCH) cmd/server/main.go
override_dh_auto_install:
override_dh_auto_test:
```

Backlinks: [[backdoor]] · [[convoc2]] · [[Parrot Gap Analysis]]
