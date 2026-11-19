---
tool: "wstunnel"
build-system: "rust"
upstream: "https://github.com/erebe/wstunnel"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing wstunnel

> WebSocket/HTTP2/WebTransport tunnel for TCP/UDP — egress and DPI bypass.

Source: hacktricks feed; upstream [`erebe/wstunnel`](https://github.com/erebe/wstunnel).

## Prebuilt static binary (recommended)

Standalone binaries (no runtime dependencies) for x86_64, arm64, armv7 and more are published on the [Releases](https://github.com/erebe/wstunnel/releases) page — download the archive for your architecture, extract, and put `wstunnel` on your `$PATH`. v7+ is the Rust rewrite; the old Haskell versions (pre-7.0.0) are incompatible and only on the `haskell` branch.

## Docker

```bash
docker pull ghcr.io/erebe/wstunnel:latest
```

## Build from source

```bash
git clone https://github.com/erebe/wstunnel.git
cd wstunnel
cargo build --package wstunnel-cli
# binary at target/debug/wstunnel (use --release for production builds)
```

## Notes

- Client and server are the same binary (`wstunnel client ...` / `wstunnel server ...`); versions on both ends should match for v7's protocol.
- Android: run the release binary under Termux.
- Demo server for quick egress tests: `wstunnel client -L 'tcp://4443:10.43.0.11:443' -P demo --tls-sni-override=google.fr wss://ws.erebe.dev` then `curl -k https://localhost:4443`.

Backlinks: [[proxy]] · [[wstunnel]]
