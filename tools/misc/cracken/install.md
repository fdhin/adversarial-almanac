---
tool: "cracken"
build-system: "go"
upstream: "https://github.com/shmuelamar/cracken"
recipe: "blackarch"
source: "https://github.com/shmuelamar/cracken"
license: "MIT"
blackarch-pkgver: "v1.0.1.r0.g7a325ff"
---

# Installing cracken

> A ast password wordlist generator, Smartlist creation and password hybrid-mask analysis tool written in pure safe Rust.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cracken) (version `v1.0.1.r0.g7a325ff`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cracken    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/shmuelamar/cracken/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/shmuelamar/cracken) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[cracken]] · [[BlackArch Tools]]

