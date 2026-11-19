---
tool: "procscope"
build-system: "go"
upstream: "https://github.com/Mutasem-mk4/procscope"
recipe: "blackarch"
source: "https://github.com/Mutasem-mk4/procscope"
license: "MIT"
blackarch-pkgver: "1.1.0"
---

# Installing procscope

> Process-scoped runtime investigation tool using eBPF.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/procscope) (version `1.1.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S procscope    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Mutasem-mk4/procscope/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Mutasem-mk4/procscope) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `go`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  export CGO_ENABLED=0
  export GOFLAGS='-trimpath -mod=readonly -modcacherw'

  go build \
    -buildmode=pie \
    -ldflags "-s -w \
      -X 'github.com/Mutasem-mk4/procscope/internal/version.Version=$pkgver' \
      -X 'github.com/Mutasem-mk4/procscope/internal/version.Commit=blackarch'" \
    -o "$pkgname" \
    ./cmd/procscope
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`, `/usr/share/fish`, `/usr/share/man`, `/usr/share/zsh`

Backlinks: [[defensive]] · [[procscope]] · [[BlackArch Tools]]

