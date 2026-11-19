---
tool: "graphql-path-enum"
build-system: "go"
upstream: "https://gitlab.com/dee-see/graphql-path-enum"
recipe: "blackarch"
source: "https://gitlab.com/dee-see/graphql-path-enum"
license: "Unlicense"
blackarch-pkgver: "23.5450280"
---

# Installing graphql-path-enum

> Tool that lists the different ways of reaching a given type in a GraphQL schema.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/graphql-path-enum) (version `23.5450280`, license Unlicense).

## Arch / BlackArch

```bash
sudo pacman -S graphql-path-enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://gitlab.com/dee-see/graphql-path-enum/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://gitlab.com/dee-see/graphql-path-enum) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[graphql-path-enum]] · [[BlackArch Tools]]

