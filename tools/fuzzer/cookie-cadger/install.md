---
tool: "cookie-cadger"
build-system: "scripts"
upstream: "https://cookiecadger.com/"
recipe: "blackarch"
source: "https://cookiecadger.com/"
license: "custom:FreeBSD"
blackarch-pkgver: "1.08"
---

# Installing cookie-cadger

> An auditing tool for Wi-Fi or wired Ethernet connections.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cookie-cadger) (version `1.08`, license custom:FreeBSD).

## Arch / BlackArch

```bash
sudo pacman -S cookie-cadger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://cookiecadger.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `wireshark-cli`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[cookie-cadger]] · [[BlackArch Tools]]

