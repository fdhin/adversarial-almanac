---
tool: "evil-opencode"
build-system: "other"
upstream: "https://github.com/winmin/evil-opencode"
recipe: "blackarch"
source: "https://github.com/winmin/evil-opencode"
license: "MIT"
blackarch-pkgver: "1.19.0"
provides: ["evil-opencode"]
---

# Installing evil-opencode

> OpenCode coding-agent fork with LLM prompt guardrails removed and outbound telemetry disabled.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evil-opencode) (version `1.19.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S evil-opencode    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/winmin/evil-opencode) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bun`
  - `gcc-libs`
  - `glibc`
  - `ripgrep`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver-unguarded"

  export OPENCODE_VERSION="$pkgver"

  bun install

  cd packages/opencode
  bun run script/build.ts --single --skip-embed-web-ui
```

## Installed layout (per recipe)

- Commands: `/usr/bin/evil-opencode`

Backlinks: [[ai]] · [[evil-opencode]] · [[BlackArch Tools]]

