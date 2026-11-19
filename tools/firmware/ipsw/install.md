---
tool: "ipsw"
build-system: "go"
upstream: "https://github.com/blacktop/ipsw"
recipe: "upstream"
license: "MIT"
---

# Installing ipsw

> iOS/macOS research Swiss Army knife: IPSW/OTA download and unpack, Mach-O and dyld-cache analysis, kernelcache inspection, device services.

Source: HackTricks feed admission; upstream [`blacktop/ipsw`](https://github.com/blacktop/ipsw).

## Prebuilt / releases

Prebuilt binaries for macOS, Linux and Windows on
[GitHub Releases](https://github.com/blacktop/ipsw/releases/latest).

## Package managers

```sh
# macOS (maintainer's tap, extras build) or Homebrew core
brew install blacktop/tap/ipsw
brew install ipsw

# Linux
sudo snap install ipsw

# Windows
scoop bucket add blacktop https://github.com/blacktop/scoop-bucket.git
scoop install blacktop/ipsw
```

Also available in nixpkgs and the AUR (`ipsw`). Some commands depend on the
host OS or build variant (Frida support is a separate build).

## Building from source

Requires Go 1.26+ and a C toolchain for CGO:

```sh
git clone https://github.com/blacktop/ipsw.git
cd ipsw && make build
```

## Configuration

Reads YAML from `~/.config/ipsw/config.yaml` (`--config` to override); see
`config.example.yml` in the repo.

Backlinks: [[firmware]] · [[ipsw]]
