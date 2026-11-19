---
tool: "jondo"
build-system: "scripts"
upstream: "https://anonymous-proxy-servers.net/"
recipe: "blackarch"
source: "https://anonymous-proxy-servers.net/"
license: "BSD"
blackarch-pkgver: "00.20.001"
---

# Installing jondo

> Redirects internet traffic trough a mix of proxy servers to hide the origin of the requests.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jondo) (version `00.20.001`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S jondo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://anonymous-proxy-servers.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/java`, `/usr/share/man`, `/usr/share/menu`, `/usr/share/pixmaps`

Backlinks: [[proxy]] · [[jondo]] · [[BlackArch Tools]]

