---
name: "cwe-checker-install"
categories:
  - code-audit
---

# Installing cwe-checker

Docker (simplest) — from the GitHub container registry:

```sh
docker pull ghcr.io/fkie-cad/cwe_checker:stable
```

(`latest` tracks master; tagged releases such as `:v0.9` exist. Build your own with `docker build -t cwe_checker .` — needed on ARM hosts, since the prebuilt images are x86-only.)

Local build — needs Rust >= 1.82 and Ghidra >= 10.2:

```sh
make all GHIDRA_PATH=/path/to/ghidra_folder
```

(Omit `GHIDRA_PATH` and the installer searches the filesystem for a Ghidra install.)

Verification: `cwe_checker --help`.

Backlinks: [[code-audit]] · [[cwe-checker]]
