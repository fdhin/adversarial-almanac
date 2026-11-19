---
tool: "bing-ip2hosts"
build-system: "scripts"
upstream: "https://github.com/urbanadventurer/bing-ip2hosts/"
recipe: "blackarch"
aur-package: "bing-ip2hosts"
source: "https://github.com/urbanadventurer/bing-ip2hosts/"
license: "GPL3"
blackarch-pkgver: "1.0.5"
---

# Installing bing-ip2hosts

> Enumerates all hostnames which Bing has indexed for a specific IP address.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bing-ip2hosts) (version `1.0.5`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bing-ip2hosts    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.morningstarsecurity.com/research/bing-ip2hosts) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bind-tools`
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[bing-ip2hosts]] · [[BlackArch Tools]]

