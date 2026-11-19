---
tool: "ecapture"
build-system: "go"
upstream: "https://github.com/gojue/ecapture"
recipe: "upstream"
version: "git (2026-11-07)"
license: "see repository"
---

# Installing ecapture

> Capture SSL/TLS plaintext with eBPF—no MITM proxy or custom CA installation. Supports Linux and Android on x86_64\arm64 and loong64.

Source: kitploit feature [post](https://kitploit.com/en/posts/ecapture-cd4bb4cfe0a26892) — upstream [`gojue/ecapture`](https://github.com/gojue/ecapture).

## Prebuilt / releases

Check [upstream releases](https://github.com/gojue/ecapture/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/gojue/ecapture.git
cd ecapture
go build ./...
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07) — available via AUR — available via nixpkgs.

Backlinks: [[sniffer]] · [[ecapture]]
