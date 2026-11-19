---
name: "h26forge-install"
categories:
  - binary
---

# Installing h26forge

Prebuilt Linux binaries from the [GitHub releases](https://github.com/h26forge/h26forge/releases) page, or from source (Rust toolchain via rustup + Python 3):

```bash
git clone https://github.com/h26forge/h26forge
cd h26forge
./make.sh        # runs cargo build --release, copies the binary to the repo root
```

Windows uses `./make.bat`. Python 3 is only needed for `modify` mode (transforms); the randomize/generate/synthesize/mux modes are pure Rust.
