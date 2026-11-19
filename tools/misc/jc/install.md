---
tool: "jc"
build-system: "python"
upstream: "https://github.com/kellyjonbrazil/jc"
recipe: "upstream"
version: "git (2026-11-07)"
license: "see repository"
---

# Installing jc

> CLI tool and python library that converts the output of popular command-line tools, file-types, and common strings to JSON, YAML, or Dictionaries. This allows piping of output to tools like jq and simplifying automation scripts.

Source: upstream [`kellyjonbrazil/jc`](https://github.com/kellyjonbrazil/jc); discovered via the awesome-oneliner-bugbounty review.

## Arch / BlackArch

```bash
sudo pacman -S jc
```

## Nix (any distro)

```bash
nix run nixpkgs#jc
```

## From source

```bash
git clone https://github.com/kellyjonbrazil/jc.git
cd jc
python3 -m pip install --user .
```

Backlinks: [[misc]] · [[jc]]
