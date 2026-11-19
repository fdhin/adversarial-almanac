---
tool: "fuzzowski"
build-system: "scripts"
upstream: "https://github.com/nccgroup/fuzzowski"
recipe: "blackarch"
source: "https://github.com/nccgroup/fuzzowski"
license: "GPL"
blackarch-pkgver: "41.e39f665"
---

# Installing fuzzowski

> A Network Protocol Fuzzer made by NCCGroup based on Sulley and BooFuzz.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fuzzowski) (version `41.e39f665`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S fuzzowski    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/fuzzowski) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `attrs`, `prompt_toolkit`, `pydot`, `pygments`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[fuzzowski]] · [[BlackArch Tools]]

