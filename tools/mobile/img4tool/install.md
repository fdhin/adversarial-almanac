---
tool: "img4tool"
build-system: "autotools"
upstream: "https://github.com/tihmstar/img4tool"
recipe: "upstream"
license: "see repository"
---

# Installing img4tool

> Tool for manipulating Apple IMG4, IM4M and IM4P files: extract, decrypt, create and verify signed firmware containers.

Source: HackTricks feed admission; upstream [`tihmstar/img4tool`](https://github.com/tihmstar/img4tool).

## Package managers

Available in nixpkgs (`nix-env -iA nixpkgs.img4tool`). Not in Arch/BlackArch
repos (checked 2026-11-13).

## Building from source

Dependencies:

- Build system: `autoconf`, `automake`, `libtool`, `pkg-config`
- Libraries: `openssl`, `libplist-2.0`
- tihmstar's [libgeneral](https://github.com/tihmstar/libgeneral) (build/install first)

```bash
git clone https://github.com/tihmstar/img4tool.git
cd img4tool
./autogen.sh
make
sudo make install
```

Build `libgeneral` the same way (`./autogen.sh && make && sudo make install`)
beforehand.

Backlinks: [[mobile]] · [[img4tool]]
