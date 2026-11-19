---
tool: "zond"
build-system: "go"
upstream: "https://github.com/zond-rs/zond"
recipe: "blackarch"
nix-package: "zond"
aur-package: "zond"
source: "https://github.com/zond-rs/zond"
license: "AGPL-3.0-or-later, CC-BY-SA-4.0"
blackarch-pkgver: "0.19.0.r11.gc57ba92"
---

# Installing zond

> Network scanner that finds hosts, ports and services and matches them against distribution security data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zond) (version `0.19.0.r11.gc57ba92`, license AGPL-3.0-or-later, CC-BY-SA-4.0).

## Arch / BlackArch

```bash
sudo pacman -S zond    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/zond-rs/zond/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/zond-rs/zond) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
# ring's C objects are built with LTO, which rust-lld cannot link.
  # See https://archlinux.org/todo/lto-fat-objects/ for more details.
  CFLAGS+=' -ffat-lto-objects'
  cd $pkgname

  cargo build --release --locked

  SOURCE_DATE_EPOCH="$(git log -1 --format=%ct)" \
    target/release/$pkgname __generate target/assets
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`, `/usr/share/fish`, `/usr/share/man`, `/usr/share/zsh`

Backlinks: [[scanner]] · [[zond]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#zond    # ad-hoc run, no install
nix profile install nixpkgs#zond   # or persist it
```
