---
tool: "host-extract"
build-system: "scripts"
upstream: "https://code.google.com/p/host-extract/"
recipe: "blackarch"
source: "https://code.google.com/p/host-extract/"
license: "GPL3"
blackarch-pkgver: "8.0134ad7"
---

# Installing host-extract

> Ruby script tries to extract all IP/Host patterns in page response of a given URL and JavaScript/CSS files of that URL.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/host-extract) (version `8.0134ad7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S host-extract    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/host-extract/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-http-cookie`
  - `ruby-mechanize`
  - `ruby-net-http-digest_auth`
  - `ruby-ntlm-http`
  - `ruby-rubyntlm`
  - `ruby-webrick`
  - `ruby-webrobots`
  - `rubygems`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[host-extract]] · [[BlackArch Tools]]

