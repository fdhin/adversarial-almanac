---
tool: "stratum-c2"
build-system: "rust"
upstream: "https://github.com/lame-projects/stratum-c2"
recipe: "upstream"
version: "git (2026-10-27)"
license: "see repository"
---

# Installing stratum-c2

> Cloud dead-drop C2 framework — RSA-4096 + AES-256-GCM, 5 cloud providers, Rust-only agents, P2P mesh, persistence engine, credential harvesting

Source: kitploit directory; upstream [`lame-projects/stratum-c2`](https://github.com/lame-projects/stratum-c2).

## Prebuilt / releases

Check [upstream releases](https://github.com/lame-projects/stratum-c2/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/lame-projects/stratum-c2.git
cd stratum-c2
cargo build --release
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[backdoor]] · [[stratum-c2]]
