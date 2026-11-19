---
tool: "gitleaks"
build-system: "go"
upstream: "https://github.com/gitleaks/gitleaks"
recipe: "upstream"
license: "see repository"
---

# Installing gitleaks

> Scan git repositories, directories, files or stdin for hardcoded secrets — passwords, API keys and tokens — past or present.

Source: hacktricks feed; upstream [`gitleaks/gitleaks`](https://github.com/gitleaks/gitleaks) (formerly `zricethezav/gitleaks`, same repository).

## Arch Linux

```bash
sudo pacman -S gitleaks
```

## macOS / Linux (Homebrew)

```bash
brew install gitleaks
```

## Docker

```bash
docker pull zricethezav/gitleaks:latest
docker run -v ${PWD}:/path zricethezav/gitleaks:latest git -v /path
```

An official `ghcr.io/gitleaks/gitleaks` image is also published.

## Any other Linux distro

Prebuilt binaries for common platforms are on the
[releases page](https://github.com/gitleaks/gitleaks/releases), or build from
source with Go installed:

```bash
git clone https://github.com/gitleaks/gitleaks.git
cd gitleaks
make build
```

`go install github.com/gitleaks/gitleaks/v8@latest` also works.

## CI / pre-commit hooks

Gitleaks ships as a GitHub Action ([gitleaks/gitleaks-action](https://github.com/gitleaks/gitleaks-action))
and a [pre-commit](https://pre-commit.com/) hook (`repo: https://github.com/gitleaks/gitleaks`,
hook id `gitleaks` or `gitleaks-docker`); `SKIP=gitleaks` bypasses the hook
for a single commit.

Backlinks: [[code-audit]] · [[gitleaks]]
