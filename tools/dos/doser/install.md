---
tool: "doser"
build-system: "go"
upstream: "https://github.com/Quitten/doser.go"
recipe: "upstream"
license: "custom:unknown"
---

# Installing doser

> DoS tool for HTTP requests — parallel GET/POST flood with throttle and error reporting (hulk-inspired, Go).

Source: howtohunt feed; upstream [`Quitten/doser.go`](https://github.com/Quitten/doser.go). No LICENSE file ships with the repo (checked 2026-11-13).

## From source

Single-file Go program, standard toolchain build:

```bash
git clone https://github.com/Quitten/doser.go.git
cd doser.go
go build doser.go
./doser -t 999 -g 'http://target.example.com'
```

## Not packaged

No distro package and no Go module path (the repo predates go.mod) —
`go install` does not apply; build from clone (checked 2026-11-13).

Backlinks: [[cracker]] · [[doser]]
