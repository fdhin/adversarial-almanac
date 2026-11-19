---
tool: "brainstorm"
build-system: "scripts"
upstream: "https://github.com/s7x/brainstorm"
recipe: "blackarch"
source: "https://github.com/s7x/brainstorm"
license: "custom:unknown"
blackarch-pkgver: "38.c5b66c6"
---

# Installing brainstorm

> A smarter web fuzzing tool that combines local LLM models and ffuf to optimize directory and file discovery.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brainstorm) (version `38.c5b66c6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S brainstorm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/s7x/brainstorm) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ffuf`
  - `ollama`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[brainstorm]] · [[BlackArch Tools]]

