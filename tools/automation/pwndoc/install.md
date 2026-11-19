---
tool: "pwndoc"
build-system: "node"
upstream: "https://github.com/pwndoc/pwndoc"
recipe: "upstream"
license: "MIT"
---

# Installing pwndoc

> PwnDoc — pentest reporting application: collaborative findings, reusable vulnerability data, customizable DOCX reports.

Source: pentest-book feed; upstream [`pwndoc/pwndoc`](https://github.com/pwndoc/pwndoc). Full docs at [pwndoc.github.io/pwndoc](https://pwndoc.github.io/pwndoc/#/installation).

## Deploy with Docker (standard path)

PwnDoc runs as four containers — backend, frontend, database, and an optional LanguageTool container for spellcheck. All operations go through the `pwndoc-cli` wrapper, which orchestrates Docker Compose behind the scenes.

```bash
git clone https://github.com/pwndoc/pwndoc.git
cd pwndoc
./pwndoc-cli up
```

Outcome: pulls the pre-built images and starts all containers; the application is then at **https://localhost:8443** with the API at **https://localhost:8443/api**. Development (`./pwndoc-cli up --dev`, hot-reload on port 8081) and `--prod-local` (build from local source) variants use the same wrapper.

## Enable the LanguageTool spellcheck container

```bash
./pwndoc-cli up --with-lt
./pwndoc-cli lt-apikey
```

Outcome: starts the optional LanguageTool container and prints the generated API key; enter it in **Settings → Spellcheck** together with the LanguageTool URL (`http://languagetools:8010/v2`).

## Lifecycle and updates

```
./pwndoc-cli stop      # stop containers, keep data
./pwndoc-cli down      # stop and remove containers
./pwndoc-cli logs      # follow container logs
./pwndoc-cli update    # pull latest images and recreate changed containers
```

Outcome: standard container lifecycle via the wrapper — `update` pulls the latest pre-built images (or `git pull` + rebuild in dev/prod-local modes) and verifies all containers come back up; add `--backend-only`/`--frontend-only` to target one service.

## Not yet packaged

No distro packages PwnDoc (checked 2026-11-13) — Docker with the `pwndoc-cli` wrapper above is the only supported deployment.

Backlinks: [[automation]] · [[pwndoc]]
