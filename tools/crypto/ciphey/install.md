---
tool: "ciphey"
build-system: "rust"
upstream: "https://github.com/bee-san/Ciphey"
recipe: "upstream"
license: "MIT"
---

# Installing ciphey

> Automatic decoder/cracker: paste encoded or encrypted text, get the plaintext and the decoder path.

Source: upstream [`bee-san/Ciphey`](https://github.com/bee-san/Ciphey). The current line is a Rust rewrite of the earlier Python Ciphey (last Python version: 5.14.0) — distro packages may still build the older Python implementation, so check what `ciphey --help` reports after installing.

## Prebuilt / releases

[Upstream releases](https://github.com/bee-san/Ciphey/releases/latest) carry prebuilt binaries for Linux (x86_64), macOS (Intel and Apple silicon) and Windows (x86_64), each with a `.sha256` checksum.

## Cargo (any platform with a Rust toolchain)

```bash
cargo install ciphey
```

## Arch Linux

```bash
yay -S ciphey    # AUR
```

## Nix (works on any distro)

```bash
nix run nixpkgs#ciphey    # ad-hoc run, no install
nix profile install nixpkgs#ciphey
```

## Build from source

```bash
git clone https://github.com/bee-san/Ciphey
cd Ciphey
cargo build --release    # the binary is target/release/ciphey
```

The optional MCP server for AI assistants builds behind a feature flag:

```bash
cargo install --git https://github.com/bee-san/Ciphey ciphey --features mcp --bin ciphey-mcp
```

Backlinks: [[crypto]] · [[ciphey]]
