---
tool: "httpforge"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/98109/HTTPForge-11.02.01.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/98109/HTTPForge-11.02.01.html"
license: "custom:unknown"
blackarch-pkgver: "11.02.01"
---

# Installing httpforge

> A set of shell tools that let you manipulate, send, receive, and analyze HTTP messages. These tools can be used to test, discover, and assert the security of Web servers, apps, and sites. An accompanying Python library is available for extensions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httpforge) (version `11.02.01`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S httpforge    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/98109/HTTPForge.02.01.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[httpforge]] · [[BlackArch Tools]]

