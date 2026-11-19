---
name: "cuda-rarcrypt"
gentoo-category: "app-crypt"
version: "3.2-r1"
homepage: "http://www.crark.net/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cuda-rarcrypt

> Historic open-source NVIDIA CUDA brute-forcer for RAR archive passwords, built from the author's `Cuda-OpenSrc` release.

## What it's for

A 2009-era Gentoo/pentoo package that compiled the open-source CUDA RAR password cracker distributed by the cRARk author (crark.net) as `Cuda-OpenSrc.rar` — a single `rarcrypt.cu` kernel built against the NVIDIA CUDA SDK, installing the binary as `cuda-rarcrypt`. It recovered passwords of RAR-encrypted archives on NVIDIA GPUs, a GPU-accelerated counterpart to CPU tools like [[rarcrack]]. The package is historical: the source download is gone from crark.net and unarchived, pentoo removed the ebuild in 2020, and no CLI documentation survives — only the build recipe and its SDK patches. No verifiable command line remains, so no usage examples are given; for present-day RAR (including RAR5) recovery use the maintained cRARk or John the Ripper's `rar` formats after extracting hashes with `rar2john`.

*No corpus evidence — derived from upstream documentation.*
