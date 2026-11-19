---
tool: "frp"
build-system: "go"
upstream: "https://github.com/fatedier/frp"
recipe: "upstream"
version: "git (2026-11-05)"
license: "see repository"
---

# Installing frp

> A fast reverse proxy to help you expose a local server behind a NAT or firewall to the internet.

Source: kitploit feature [post](https://kitploit.com/en/posts/frp-35e23e3753a86689) — upstream [`fatedier/frp`](https://github.com/fatedier/frp).

## Prebuilt / releases

Check [upstream releases](https://github.com/fatedier/frp/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/fatedier/frp.git
cd frp
go build ./...
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07) — available via nixpkgs.

Backlinks: [[tunnel]] · [[frp]]
