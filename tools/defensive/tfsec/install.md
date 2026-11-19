---
tool: "tfsec"
build-system: "go"
upstream: "https://github.com/aquasecurity/tfsec"
recipe: "blackarch"
nix-package: "tfsec"
aur-package: "tfsec"
source: "https://github.com/aquasecurity/tfsec"
license: "MIT"
blackarch-pkgver: "v0.63.1.r408.gb692c20b6"
---

# Installing tfsec

> Security scanner for your Terraform code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tfsec) (version `v0.63.1.r408.gb692c20b6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tfsec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/aquasecurity/tfsec/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/aquasecurity/tfsec) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname/cmd/$pkgname"

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[tfsec]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#tfsec    # ad-hoc run, no install
nix profile install nixpkgs#tfsec   # or persist it
```
