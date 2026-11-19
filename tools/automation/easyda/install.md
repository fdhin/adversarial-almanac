---
tool: "easyda"
build-system: "scripts"
upstream: "https://github.com/nccgroup/easyda"
recipe: "blackarch"
source: "https://github.com/nccgroup/easyda"
license: "GPL3"
blackarch-pkgver: "7.0867f9b"
---

# Installing easyda

> Easy Windows Domain Access Script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/easyda) (version `7.0867f9b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S easyda    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/easyda) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `metasploit`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[easyda]] · [[BlackArch Tools]]

