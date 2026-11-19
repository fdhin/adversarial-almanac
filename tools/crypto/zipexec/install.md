---
tool: "zipexec"
build-system: "go"
upstream: "https://github.com/Tylous/ZipExec"
recipe: "blackarch"
source: "https://github.com/Tylous/ZipExec"
license: "MIT"
blackarch-pkgver: "19.f8d661f"
---

# Installing zipexec

> A unique technique to execute binaries from a password protected zip.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zipexec) (version `19.f8d661f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S zipexec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Tylous/ZipExec/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/Tylous/ZipExec.git
cd ZipExec
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

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

Backlinks: [[crypto]] · [[zipexec]] · [[BlackArch Tools]]

