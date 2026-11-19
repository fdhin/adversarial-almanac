---
tool: "gore"
build-system: "go"
upstream: "https://github.com/goretk/gore"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing gore

> Go library for recovering packages, functions and types from Go binaries.

Source: hacktricks feed; upstream [`goretk/gore`](https://github.com/goretk/gore) — the canonical repo (older figurophobia-hosted copies circulate in stale documentation).

## As a library (the normal way)

```bash
go get github.com/goretk/gore
```

Then import it in your analysis tool. Version-detection data updates locally with `go generate` instead of a new release.

## CLI alternative

GoRE is a library; for a ready-made command-line consumer of the same analysis use [redress](https://github.com/goretk/redress).

Also packaged in the AUR (`gore`).

## License note

Most files are BSD-licensed; some features (GOROOT extraction) are AGPL. Build with the `gore_bsd` tag to produce a purely BSD-licensed artifact — without it, your binary includes AGPL code and must be distributed as AGPL.

Backlinks: [[reversing]] · [[gore]]
