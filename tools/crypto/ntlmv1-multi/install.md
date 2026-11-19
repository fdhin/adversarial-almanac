---
tool: "ntlmv1-multi"
build-system: "scripts"
upstream: "https://github.com/evilmog/ntlmv1-multi"
recipe: "blackarch"
source: "https://github.com/evilmog/ntlmv1-multi"
license: "MIT"
blackarch-pkgver: "115.c17f17d"
provides: ["ntlm-to-des", "ntlmv1"]
---

# Installing ntlmv1-multi

> NTLMv1 Multitool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntlmv1-multi) (version `115.c17f17d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ntlmv1-multi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/evilmog/ntlmv1-multi) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- Commands: `/usr/bin/ntlm-to-des`, `/usr/bin/ntlmv1`

Backlinks: [[crypto]] · [[ntlmv1-multi]] · [[BlackArch Tools]]

