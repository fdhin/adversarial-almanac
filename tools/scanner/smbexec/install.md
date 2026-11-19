---
tool: "smbexec"
build-system: "scripts"
upstream: "https://github.com/pentestgeek/smbexec"
recipe: "blackarch"
source: "https://github.com/pentestgeek/smbexec"
license: "custom:unknown"
blackarch-pkgver: "59.a54fc14"
---

# Installing smbexec

> A rapid psexec style attack with samba tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smbexec) (version `59.a54fc14`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S smbexec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pentestgeek/smbexec) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-pkg-config`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[smbexec]] · [[BlackArch Tools]]

