---
tool: "goresolver"
build-system: "python"
upstream: "https://github.com/volexity/GoResolver"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing goresolver

> CFG-similarity symbol recovery for obfuscated Go binaries, with IDA/Ghidra importers.

Source: hacktricks feed; upstream [`volexity/GoResolver`](https://github.com/volexity/GoResolver).

## Install from PyPI (recommended)

```bash
pip install goresolver
```

## Dependency: Go

A base Go installation of 1.20.6 or newer must be on the system (see https://go.dev/dl/) — GoResolver builds its reference samples with additional Go versions it manages itself (`goresolver manage --list_available`).

## Build from source

Uses Hatch:

```bash
hatch build
pip install dist/goresolver-*.whl
```

## Disassembler plugins

IDAPython and Ghidra plugins ship in the repo's `Plugin` directory (discrete releases too). When installing, copy the chosen plugin's `ida`/`ghidra` files together with the shared `common` directory — see the plugin README.

Backlinks: [[reversing]] · [[goresolver]]
