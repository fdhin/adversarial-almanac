---
tool: "logmepwn"
build-system: "go"
upstream: "https://github.com/0xInfection/LogMePwn"
recipe: "blackarch"
source: "https://github.com/0xInfection/LogMePwn"
license: "GPL3"
blackarch-pkgver: "24.f257a8f"
---

# Installing logmepwn

> A fully automated, reliable, super-fast, mass scanning and validation toolkit for the Log4J RCE CVE-44228 vulnerability.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/logmepwn) (version `24.f257a8f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S logmepwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/0xInfection/LogMePwn/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/0xInfection/LogMePwn.git
cd LogMePwn
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

Backlinks: [[scanner]] · [[logmepwn]] · [[BlackArch Tools]]

