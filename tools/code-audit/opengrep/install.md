---
tool: "opengrep"
build-system: "ocaml"
upstream: "https://github.com/opengrep/opengrep"
recipe: "upstream"
aur-package: "opengrep"
source: "https://github.com/opengrep/opengrep/releases"
license: "LGPL-2.1"
---

# Installing opengrep

> Self-contained OCaml binaries; no runtime language dependencies.

## Quick install (Linux / macOS)

```bash
curl -fsSL https://raw.githubusercontent.com/opengrep/opengrep/main/install.sh | bash
```

## Windows (PowerShell)

```powershell
irm https://raw.githubusercontent.com/opengrep/opengrep/main/install.ps1 | iex
```

Add `-Version v1.16.0` to pin a specific release.

## Arch (AUR)

```bash
paru -S opengrep    # or yay -S opengrep
```

## Manual

Download a signed release binary from the [releases page](https://github.com/opengrep/opengrep/releases) (releases are Cosign-signed). On Windows, keep `opengrep.exe` and its DLLs in the same directory.

Backlinks: [[code-audit]] · [[opengrep]] · [[semgrep]]
