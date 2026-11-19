---
tool: "hcxtools"
build-system: "make"
upstream: "https://github.com/ZerBeA/hcxtools"
recipe: "upstream"
license: "MIT"
---

# Installing hcxtools

> Capture-to-hash conversion suite (`hcxpcapngtool`, `hcxhashtool`, `hcxpsktool`, ...) feeding hashcat and John; companion of hcxdumptool.

Source: hacktricks feed; upstream [`ZerBea/hcxtools`](https://github.com/ZerBea/hcxtools).

## Arch Linux

```bash
sudo pacman -S hcxtools
```

Upstream warns distro packages are often behind git head — build from source when a current version matters, and keep `hcxpcapngtool` in sync with your `hcxdumptool` version.

## Nix (works on any distro)

```bash
nix run nixpkgs#hcxtools    # ad-hoc run, no install
```

## Build from source

```bash
git clone https://github.com/ZerBeA/hcxtools.git
cd hcxtools
make -j $(nproc)
sudo make install                  # installs to /usr/bin
# or: sudo make install PREFIX=/usr/local
```

## Dependencies (per README)

- gcc (latest recommended), make, pkg-config
- libopenssl >= 3.0 + dev headers
- zlib + dev headers (gzip-compressed cap/pcap/pcapng input)
- libcurl >= 7.56 + dev headers (used by `whoismac` and `wlancap2wpasec`)
- Linux kernel >= 5.15 (latest longterm/stable recommended)

## Notes

- Linux only: Windows, macOS, Android, emulators and wrappers are explicitly unsupported.
- Most output files are appended to if they already exist (except pcapng/pcap/cap outputs).
- Upstream positions the toolkit for experienced users: radio technology, 802.11 and key-derivation knowledge are prerequisites.
- The capture half of the pipeline ([[hcxdumptool]]) is a separate repo/package.

Backlinks: [[wireless]] · [[hcxtools]]
