---
tool: "calicoctl"
build-system: "go"
upstream: "https://github.com/projectcalico/calico"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/c/calico"
version: "3.26.0-0parrot1"
---

# Installing calicoctl

> networking and network security solution for Kubernetes This package contains the command line tool for calico. Calico is a widely adopted, battle-tested open source networking and network security so

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install calicoctl
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss calicoctl`); otherwise build from source.

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectcalico/calico/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/projectcalico/calico
cd calico
go build ./...   # exact recipe flags below
```

## Dependencies (from debian/control)

- Build (Debian names): `ca-certificates`, `golang`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_build:
	cd calicoctl && go build -v -o bin/calicoctl -ldflags "-X github.com/projectcalico/calico/calicoctl/calicoctl/commands.VERSION=upstream/$(DEB_VERSION_UPSTREAM) -X github.com/projectcalico/calico/calicoctl/calicoctl/commands/common.VERSION=upstream/$(DEB_VERSION_UPSTREAM)" "./calicoctl/calicoctl.go"
override_dh_auto_test:
override_dh_dwz:
```

Backlinks: [[defensive]] · [[calicoctl]] · [[Parrot Gap Analysis]]
