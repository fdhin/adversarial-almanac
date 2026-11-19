---
tool: "rusthound"
build-system: "go"
upstream: "https://github.com/NH-RED-TEAM/RustHound"
recipe: "blackarch"
source: "https://github.com/NH-RED-TEAM/RustHound"
license: "MIT"
blackarch-pkgver: "1.1.69.r4.g241751c"
---

# Installing rusthound

> Active Directory data collector for BloodHound written in Rust.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rusthound) (version `1.1.69.r4.g241751c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rusthound    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/NH-RED-TEAM/RustHound/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/NH-RED-TEAM/RustHound) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`

## Exact build steps from the recipe

```bash
cd $_pkgname

  # The pinned `time 0.3.30` does not build with current Rust (E0282). Bump it
  # to a compatible release so the crate compiles; the rest of the lockfile is
  # kept as upstream shipped it.
  cargo update -p time --precise 0.3.36
  # ring 0.17.x assembly does not link with the default rust-lld on Rust 1.98;
  # disable the bundled lld so the system GNU ld is used, and build the ring
  # C/asm with default visibility so the ring_core_* symbols are exported.
  cargo update -p ring@0.17.7 --precise 0.17.11
  CFLAGS="-fPIC -fvisibility=default" RUSTFLAGS="-C link-self-contained=-linker -C linker-features=-lld" cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[rusthound]] · [[BlackArch Tools]]

