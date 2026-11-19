---
tool: "talon"
build-system: "go"
upstream: "https://github.com/optiv/Talon"
recipe: "blackarch"
source: "https://github.com/optiv/Talon"
license: "MIT"
blackarch-pkgver: "v3.1.r1.g8acc175"
---

# Installing talon

> A password guessing tool that targets the Kerberos and LDAP services within the Windows Active Directory environment.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/talon) (version `v3.1.r1.g8acc175`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S talon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/optiv/Talon/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/optiv/Talon.git
cd Talon
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod tidy
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

Backlinks: [[cracker]] · [[talon]] · [[BlackArch Tools]]

