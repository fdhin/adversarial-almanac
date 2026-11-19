---
name: "authoscope-install"
categories:
  - cracker
---

# Installing authoscope

Arch Linux (also available in nixpkgs as `authoscope`):

```sh
pacman -S authoscope
```

macOS:

```sh
brew install authoscope
```

From source — needs [rustup](https://rustup.rs/) and `libssl-dev`, then from the repo root:

```sh
cargo install
```

Runtime defaults live in `~/.config/authoscope.toml`: a custom `user_agent` under `[runtime]`, or `rlimit_nofile` for very high concurrency (raising the fd limit needs `sudo setcap 'CAP_SYS_RESOURCE=+ep' /usr/bin/authoscope`).

Verification: `authoscope --help`.

Backlinks: [[cracker]] · [[authoscope]]
