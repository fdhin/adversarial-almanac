---
tool: "steampipe"
build-system: "go"
upstream: "https://github.com/turbot/steampipe"
recipe: "upstream"
license: "AGPL3"
---

# Installing steampipe

> SQL over live cloud/SaaS APIs — 2000+ tables behind a Postgres-compatible single binary.

Source: cloudpentestcheatsheets feed; upstream [`turbot/steampipe`](https://github.com/turbot/steampipe).

## macOS (Homebrew)

```sh
brew install turbot/tap/steampipe
```

## Linux / Windows (WSL2)

```sh
sudo /bin/sh -c "$(curl -fsSL https://steampipe.io/install/steampipe.sh)"
```

Prebuilt binaries for other platforms are on the [downloads](https://steampipe.io/downloads) page.

## Build from source

```sh
git clone git@github.com:turbot/steampipe
cd steampipe
make
```

The binary lands in `/usr/local/bin/steampipe` unless `OUTPUT_DIR` is set.

## First steps

```sh
steampipe plugin install hackernews
steampipe query
```

Plugins install per-service (aws, azure, gcp, kubernetes, github, ...) from hub.steampipe.io; each uses the provider's native credential chain.

## Notes

- Distributions beyond the CLI: Postgres FDWs, SQLite extensions, and standalone export binaries — see steampipe.io/docs.
- Core is AGPL-3.0; the hosted Turbot Pipes service is commercial and optional.

Backlinks: [[scanner]] · [[steampipe]]
