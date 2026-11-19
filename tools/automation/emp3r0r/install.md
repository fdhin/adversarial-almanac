---
tool: "emp3r0r"
build-system: "go"
upstream: "https://github.com/jm33-m0/emp3r0r"
recipe: "blackarch"
source: "https://github.com/jm33-m0/emp3r0r.git"
license: "MIT"
blackarch-pkgver: "v5.6.0.r6.g342eecdf"
---

# Installing emp3r0r

> Linux post-exploitation framework made by linux user.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/emp3r0r) (version `v5.6.0.r6.g342eecdf`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S emp3r0r    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/jm33-m0/emp3r0r/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/jm33-m0/emp3r0r.git
cd emp3r0r
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `tmux`

## Exact build steps from the recipe

```bash
cd $pkgname

  (build_cmd 'cc' emp3r0r-cc) &&
  (build_cmd 'cat' emp3r0r-cat) &&
  (build_cmd 'agent' stub.exe) &&
  (GOOS=windows build_cmd 'agent' stub-win.exe)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[emp3r0r]] · [[BlackArch Tools]]

