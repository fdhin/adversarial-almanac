---
name: "rainbowcrack"
categories:
  - blackarch-cracker
version: "1.8"
homepage: "http://project-rainbowcrack.com/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2020-09-10"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/rainbowcrack"
github-stars: 17
github-pushed: "2013-12-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rainbowcrack

> The classic rainbow-table suite: generate (`rtgen`), sort (`rtsort`) and look up (`rcrack`) precomputed tables for instant hash cracking.

## What it's for

RainbowCrack implements Philippe Oechslin's time-memory trade-off: instead of brute-forcing each hash, you precompute rainbow tables once for a chosen hash algorithm, charset and plaintext length range, and afterwards crack any hash covered by those tables in seconds. The suite shipped as `rtgen` (table generation, interruptible and resumable), `rtsort` (tables must be sorted before lookup), `rcrack` (lookup against a single hash, a hash list, or LM/NTLM hashes from a pwdump file), plus `rt2rtc`/`rtc2rt` (convert between raw `.rt` and ~50%-smaller compact `.rtc` formats) and `rtmerge`. Hash algorithms beyond the classics (LM, NTLM, MD5, SHA-1) — including MySQL and Cisco PIX — are provided by the algorithm library, and 1.7/1.8 add NVIDIA CUDA and AMD OpenCL GPU acceleration. Tables are the trade-off's cost: covering a large keyspace takes days of precomputation and disk space, but is done once and shared forever; anything outside the table's charset/length range is simply not crackable.

## Typical use cases

### Generate a rainbow table

Precompute one table for a hash algorithm, charset and plaintext length range.

```
rtgen md5 loweralpha 1 7 0 1000 1000 0
```

Outcome: computes rainbow chains and writes `md5_loweralpha#1-7_0_1000x1000_0.rt` (the filename encodes every parameter — don't rename it); the run prints chain progress and can be stopped with Ctrl+C and resumed by re-running the identical command. A full table set needs several `rtgen` runs with different `table_index` values (0, 1, 2, ...).

### Sort generated tables before lookup

Make the raw tables searchable — `rcrack` only accepts sorted tables.

```
rtsort md5_loweralpha#1-7_0_1000x1000_0.rt
```

Outcome: loads the table, sorts the chains by end point and writes the result back in place (takes several minutes per large table; low-memory machines need free disk equal to the file size for the external sort).

### Crack a single hash

Look up one hash against every table in a directory.

```
rcrack . -h 5d41402abc4b2a76b9719d911017c592
```

Outcome: searches all `*.rt`/`*.rtc` tables in the given path, printing per-table statistics and finally `plaintext` for the hash if covered (shown as `<not found>` otherwise); cracking takes seconds when tables are cached in memory.

### Crack a batch of LM/NTLM hashes from a pwdump file

Feed a whole password dump into the lookup.

```
rcrack /path/to/tables -ntlm pwfile.txt
```

Outcome: reads NTLM hashes (use `-lm` for LanManager hashes) from the pwdump-format file, cracks each against the tables and reports every recovered plaintext; LM results for mixed-case passwords may need case correction, which NTLM side-by-side lookup resolves.

### Shrink tables into the compact format

Convert raw tables to the `.rtc` compact format to halve storage.

```
rt2rtc md5_loweralpha#1-7_0_1000x1000_0.rt
```

Outcome: writes a `.rtc` file roughly 50% smaller that `rcrack` reads transparently alongside `.rt` (`rtc2rt` converts back; `-s`/`-e` tune how many bits of the start/end points are kept).

*No corpus evidence — derived from upstream documentation.*
