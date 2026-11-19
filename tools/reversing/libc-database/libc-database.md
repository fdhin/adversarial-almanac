---
name: "libc-database"
categories:
  - blackarch-reversing
version: "45.69815cd"
homepage: "https://github.com/niklasb/libc-database"
packaged-by: [blackarch]
blackarch-builddate: "2020-04-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "niklasb/libc-database"
github-stars: 1879
github-pushed: "2026-10-24"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# libc-database

> Offline database of libc builds: identify a target's libc from leaked symbol offsets, then get exploit-ready offsets and the matching files.

## What it's for

When attacking a remote process you usually cannot read the target's libc directly — you leak only runtime addresses of a few symbols, of which only the last 12 bits (page-level randomization) are stable. This tool (niklasb's libc-database) maintains a local database of libc builds from many distributions and answers the two questions that follow: which libc build matches the leaked nibbles, and where do `system`, `/bin/sh` and other needed symbols sit in that build. It then downloads the exact matching files for local analysis. On BlackArch the repository scripts are exposed as `libc-get`, `libc-add`, `libc-download`, `libc-dump`, `libc-find` and `libc-identify` (run from a source clone they are `./get`, `./find`, and so on); a hosted web frontend over the same database is available at libc.rip. It does not write exploits — it supplies the offsets for one.

## Typical use cases

### Build the local database

Fetch a set of distro libcs and index their symbol offsets; re-running updates without re-downloading existing entries.

```
libc-get ubuntu debian
```

Outcome: downloads and indexes every Ubuntu and Debian libc package into the local database. Run `libc-get` bare to list categories, or `libc-get all` for everything (large download).

### Identify the libc from leaked symbol offsets

Turn a handful of partial addresses into candidate libc builds. Offsets are the last three hex digits of each leaked symbol address.

```
libc-find printf 260 puts f30
```

Outcome: prints the database IDs (e.g. `archive-glibc (libc6_2.19-10ubuntu2_i386)`) of every build whose `printf` and `puts` offsets end in `260` and `f30`; several hits mean you should leak one more symbol to disambiguate.

### Identify the libc from a leaked start address

Use the classic leak of the return address into `__libc_start_main`.

```
libc-find __libc_start_main_ret a83
```

Outcome: lists all builds where that return offset ends in `a83`, typically narrowing the target to one libc version and package.

### Identify a libc by hash

Match an exact file you already have (target system's libc copy, firmware extraction) instead of by offsets.

```
libc-identify bid=ebeabf5f7039f53748e996fc976b4da2d486a626
```

Outcome: prints the database ID of the matching build; BuildID (`bid=`), MD5 (`md5=`), SHA1 (`sha1=`) and SHA256 (`sha256=`) are accepted, and a direct path like `libc-identify /usr/lib/libc.so.6` checks a local library.

### Dump exploit offsets for the matched build

Get the addresses your payload needs once the libc is identified.

```
libc-dump libc6_2.19-0ubuntu6.6_i386
```

Outcome: prints key offsets such as `offset_system`, `offset_dup2`, `offset_str_bin_sh` and `__libc_start_main_ret`; extra symbol names can be passed as further arguments.

### Download the matched libc files

Fetch the exact build for offline analysis or local debugging of the exploit.

```
libc-download libc6_2.23-0ubuntu10_amd64
```

Outcome: downloads the package, extracts it and saves `libc.so.6`, the dynamic loader and related libraries under `libs/libc6_2.23-0ubuntu10_amd64/`.

### Add a custom libc to the database

Index a libc you obtained from the target itself when no distro build matches.

```
libc-add /usr/lib/libc-2.21.so
```

Outcome: the given library is indexed locally and immediately searchable with `libc-find` and dumpable with `libc-dump`.

*No corpus evidence — derived from upstream documentation.*
