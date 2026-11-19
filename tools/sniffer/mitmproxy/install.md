---
tool: "mitmproxy"
build-system: "python"
upstream: "https://mitmproxy.org"
recipe: "arch-official/extra"
nix-package: "mitmproxy"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/mitmproxy/-/raw/main/PKGBUILD"
version: "12.3.1"
license: "unknown"
---

# Installing mitmproxy

> SSL-capable man-in-the-middle HTTP proxy

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/mitmproxy/-/raw/main/PKGBUILD) (version `12.3.1`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S mitmproxy
```

## Kali / Debian-based — same package name

```bash
sudo apt install mitmproxy
```

## Nix (works on any distro)

```bash
nix run nixpkgs#mitmproxy    # ad-hoc run, no install
nix profile install nixpkgs#mitmproxy   # or persist it
```

## Any other Linux distro

**B. From source:**

```bash
git clone https://github.com/mitmproxy/mitmproxy
cd mitmproxy
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Exact build steps from the recipe

```bash
cd "$pkgname"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[sniffer]] · [[mitmproxy]] · [[Kali Gap Analysis]]
