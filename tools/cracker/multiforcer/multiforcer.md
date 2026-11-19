---
name: "multiforcer"
categories:
  - cracker
version: "1.31-1parrot1"
homepage: "https://sourceforge.net/projects/cryptohaze/"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "foreni-packages/multiforcer"
github-stars: 1
github-pushed: "2015-09-21"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# multiforcer

> Cryptohaze Multiforcer as packaged for Parrot/Kali — the CUDA + OpenCL + CPU build of the GPU brute forcer.

## What it's for

This is the same Cryptohaze MFN brute forcer as the BlackArch [[cryptohazemultiforcer]] package (version 1.31, charset-driven brute force over large hash lists — see that note for the core `-h/-f/-c/-u`, per-position charset, and network clustering patterns). The difference is the binary: this build links CUDA, OpenCL and a CPU engine at once, so it also runs on AMD GPUs and on hosts with no GPU at all. The linked GitHub repository is a Debian/Kali packaging mirror carrying the 1.31 binaries, charset files and test hashes; the project itself went dormant around 2013–2015.

## Typical use cases

### Run without any GPU

Crack on a host with no CUDA or OpenCL device.

```
multiforcer -h MD5 -f hashes.txt -c charsetnumeric --min 1 --max 4 --nocuda --noopencl --cputhreads 4
```

Outcome: walks the keyspace on CPU threads only, shows the ncurses status display (`p` pauses, `q` quits) under the `Cryptohaze MFN 1.31` banner, and prints every recovered pair as `<hash>:<password>` on the console.

### Force the OpenCL backend on an AMD GPU

Select platform and device explicitly.

```
multiforcer -h NTLM -f hashes.txt -c charset.txt --min 1 --max 8 --nocuda --openclplatform=0 --opencldevice=0 --bfi_int
```

Outcome: runs only OpenCL threads on the selected platform/device; `--bfi_int` enables the AMD BFI_INT byte-permute optimization for better Radeon throughput. Use `--noopencl` to pin a run to CUDA instead, with `-d` to choose CUDA device IDs.

*No corpus evidence — derived from upstream documentation.*
