---
tool: "rubilyn"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/backdoor.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/backdoor.html"
license: "custom:unknown"
blackarch-pkgver: "0.0.1"
---

# Installing rubilyn

> 64bit Mac OS-X kernel rootkit that uses no hardcoded address to hook the BSD subsystem in all OS-X Lion & below. It uses a combination of syscall hooking and DKOM to hide activity on a host.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rubilyn) (version `0.0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rubilyn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/backdoor.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[rubilyn]] · [[BlackArch Tools]]

