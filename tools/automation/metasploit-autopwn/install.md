---
tool: "metasploit-autopwn"
build-system: "scripts"
upstream: "https://github.com/hahwul/metasploit-autopwn"
recipe: "blackarch"
source: "https://github.com/hahwul/metasploit-autopwn"
license: "custom:unknown"
blackarch-pkgver: "12.09320cc"
---

# Installing metasploit-autopwn

> db_autopwn plugin of metasploit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/metasploit-autopwn) (version `12.09320cc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S metasploit-autopwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hahwul/metasploit-autopwn) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `metasploit`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[metasploit-autopwn]] · [[BlackArch Tools]]

