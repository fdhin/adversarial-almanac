---
tool: "merlin-server"
build-system: "scripts"
upstream: "https://github.com/Ne0nd0g/merlin"
recipe: "blackarch"
source: "https://github.com/Ne0nd0g/merlin"
license: "GPL3"
blackarch-pkgver: "1.3.0"
---

# Installing merlin-server

> Merlin is a cross-platform post-exploitation HTTP/2 Command & Control server and agent written in golang.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/merlin-server) (version `1.3.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S merlin-server    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Ne0nd0g/merlin) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `p7zip`
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[merlin-server]] · [[BlackArch Tools]]

