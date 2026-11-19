---
tool: "apacket"
build-system: "go"
upstream: "https://github.com/Acey9/apacket"
recipe: "blackarch"
source: "https://github.com/Acey9/apacket"
license: "custom:unknown"
blackarch-pkgver: "374.16e7036"
provides: ["sapacket"]
---

# Installing apacket

> Sniffer syn and backscatter packets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apacket) (version `374.16e7036`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S apacket    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Acey9/apacket/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Acey9/apacket) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `go-pie`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
# Package does not use the new Golang build process because the repo was deleted
  # and tests can not be made.
  mkdir -p "src/$_url" && rm -rf "src/$_url" && mv apacket "src/$_url" &&
    cd "src/$_url"

  GOPATH="$srcdir" go get -d -t $_url
  GOPATH="$srcdir" go get -d -t $_url2
```

## Installed layout (per recipe)

- Commands: `/usr/bin/sapacket`

Backlinks: [[networking]] · [[apacket]] · [[BlackArch Tools]]

