---
tool: "gspy"
build-system: "go"
upstream: "https://github.com/Mutasem-mk4/gspy"
recipe: "blackarch"
source: "https://github.com/Mutasem-mk4/gspy"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.1.1"
---

# Installing gspy

> Forensic goroutine-to-syscall inspector for live Go processes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gspy) (version `0.1.1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S gspy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Mutasem-mk4/gspy/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Mutasem-mk4/gspy) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `go`
- **System libraries** (Arch name — translate for your distro):
  - `bpf`
  - `clang`
  - `glibc`
  - `libbpf`
  - `linux-api-headers`
  - `llvm`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  export CGO_ENABLED=0
  export PATH=$PATH:$(go env GOPATH)/bin

  # Generate vmlinux.h from running kernel BTF if available
  if [ -f /sys/kernel/btf/vmlinux ]; then
    bpftool btf dump file /sys/kernel/btf/vmlinux format c > bpf/vmlinux.h
  fi

  make generate
  make build VERSION=$pkgver
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[forensic]] · [[gspy]] · [[BlackArch Tools]]

