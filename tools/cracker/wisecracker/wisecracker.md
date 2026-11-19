---
name: "wisecracker"
gentoo-category: "app-crypt"
version: "1.0-r1"
homepage: "http://selectiveintellect.com/wisecracker.html"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: cmake
license: GPL-3
meta-checked: "2026-11-09"
github-repo: "vikasnkumar/wisecracker"
github-stars: 49
github-pushed: "2026-06-04"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# wisecracker

> OpenCL + MPI framework for distributed brute-force cryptanalysis, shipping the `wisecrackmd5` sample cracker.

## What it's for

Wisecracker is a C library and build framework for brute-force cryptanalysis jobs that are too big for one machine: it splits the keyspace across nodes with MPI and drives each node's CPUs and GPUs through OpenCL kernels, so the same code scales from a laptop with an Intel runtime to a cluster of NVIDIA/AMD cards (an AWS GPU setup script is included). Researchers either run the bundled `wisecrackmd5` tool out of the box or link `libwisecracker` and write their own brute-force application against the framework API, keeping the distribution and device plumbing out of their code. It is 64-bit-only 2012-era software: expect to provide an OpenCL SDK yourself, and note that it targets offline keyed searches, not online service attacks.

## Typical use cases

### Brute-force an MD5 over all local OpenCL devices

Search the full keyspace of a fixed-length string for a known MD5 digest.

```
wisecrackmd5 -M 2c9341ca4cf3d87b9e4eb905d6a3ec45 -N 8 -C alnum
```

Outcome: enumerates every candidate of the given length over the chosen charset (`alnum`, `alpha`, `digit`, `special`, `alnumspl`) across all detected OpenCL devices and logs the matching plaintext when found; `-g` or `-c` restrict work to GPUs or CPUs, `-m N` caps the number of devices used, and `-v 0..3` sets the log level.

### Skip ahead with a known plaintext prefix

Start the search from candidates sharing a known prefix instead of the start of the keyspace.

```
wisecrackmd5 -M 2c9341ca4cf3d87b9e4eb905d6a3ec45 -N 8 -C alnum -p Test
```

Outcome: prunes the search space to strings beginning with the suggested prefix (the example hash is of the 8-character alnum string starting with that prefix), dramatically shortening the run when part of the plaintext is already known; the prefix option requires `-M`.

### Run a custom OpenCL kernel through the framework

Substitute your own cryptanalysis primitive for the bundled MD5 one.

```
wisecrackmd5 -M <target-digest> -f custom_kernel.cl
```

Outcome: loads the supplied OpenCL source file instead of the built-in MD5 kernel, so a new algorithm (from the framework's API the kernel just maps a candidate to a digest) can be distributed across the cluster without touching the C code.

*No corpus evidence — derived from upstream documentation.*
