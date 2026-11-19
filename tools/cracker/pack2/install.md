---
tool: "pack2"
build-system: "rust"
upstream: "https://github.com/hops/pack2"
recipe: "kali-debian"
version: "0.1.0~git20200929.da4b245-0kali4"
---

# Installing pack2

> Password analysis and cracking kit 2 This package contains a replacement for iphelix's PACK. This is a work in progress. Not all features are available and while being similar some will differ slightly. . PACK was developed in order to aid in a password cracking competition "Crack Me If You Can" that occurred during Defcon 2010. The goal of this toolkit is to aid in preparation for the "better than bruteforce" password attacks by analyzing common ways that people create passwords. After the analysis stage, the statistical database can be used to generate attack masks for tools such as oclHashcat. . NOTE: This tool itself can not crack passwords, but helps other tools crack more passwords faster.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/pack2) (version `0.1.0~git20200929.da4b245-0kali4`).

## Kali / Debian-based

```bash
sudo apt install pack2
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss pack2`); otherwise build from source below.

## Any other Linux distro

**A. Prebuilt binary:** check [upstream releases](https://github.com/hops/pack2/releases).

**B. From source (needs `cargo`):**

```bash
git clone https://github.com/hops/pack2
cd pack2
cargo build --release
# binary in target/release/
```

## Dependencies (from debian/control)

- Build (Debian names): `ca-certificates`, `cargo`, `librust-regex-automata-dev`, `librust-bstr-dev`, `librust-hashbrown-dev`, `librust-structopt-dev`, `python3`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
export CARGO_HOME=$(CURDIR)/debian/cargo_home
CARGO=/usr/share/cargo/bin/cargo
	dh $@
execute_after_dh_auto_configure:
override_dh_auto_build:
override_dh_auto_test:
	rm -rf $(CURDIR)/debian/cargo_home
override_dh_dwz:
```

Backlinks: [[cracker]] · [[pack2]] · [[Kali Gap Analysis]]
