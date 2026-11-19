---
name: "oclgausscrack"
categories:
  - gpu
version: "1.3-1kali3"
homepage: "https://github.com/jsteube/oclGaussCrack"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: make
meta-checked: "2026-11-09"
github-repo: "jsteube/oclGaussCrack"
github-stars: 20
github-pushed: "2021-12-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# oclgausscrack

> OpenCL cracker for the Gauss malware's encrypted-payload verification hash.

## What it's for

oclGaussCrack (Jens Steube, 2012–2017) recovers the key string the Gauss malware used to encrypt its payload. Following the Kaspersky research it targets: each candidate UTF-16LE string gets a fixed salt appended, one MD5 on the CPU, and then a 10,000-round MD5 loop on the GPU; a match identifies the payload key (typically a Windows path string). The comparison hash is compiled into the OpenCL kernel — before building, the target constant in `src/oclGaussCrack.cl` must be set to the hash being attacked. Candidates are read from stdin, so dictionaries and pipe tooling drive the search. This is a research artifact tied to one 2012 malware family; for general GPU hash cracking use hashcat. Companion binaries preprocess candidate lists: gaussFilter drops entries Gauss itself would never use, and gaussCombinator concatenates two UTF-16 dictionaries in memory.

## Typical use cases

### Reproduce the Kaspersky test vector

Verify the build and GPU setup end to end.

```
echo -n "C:\Documents and Settings\john\Local Settings\Application Data\Google\Chrome\Application~dir1" | iconv -f utf8 -t utf16le > test.txt
oclGaussCrack64.bin < test.txt
```

Outcome: after "Initialization done, accepting candidates from stdin..." the GPU reports `GPU # 0: ALARM! Candidate number 0 cracked the hash!` followed by a hex dump of the matching UTF-16 key string — requires the test-vector hash uncommented in the kernel source first.

### Run a filtered, combined keyspace

Generate candidates the way Gauss derived its key material.

```
gaussFilter64.bin programfiles.dict > programfiles_filtered.dict
gaussCombinator64.bin envvars.dict programfiles_filtered.dict | oclGaussCrack64.bin
```

Outcome: gaussFilter trims UTF-16 path candidates whose first character is below 'z' (the entries Gauss skips, shrinking the keyspace), the combinator concatenates both dictionaries pair-wise in memory and streams them into the cracker; any key reproducing the verification hash triggers the ALARM line and hex dump.

### Split the workload across runs or hosts

Use the skip/limit parameters for sharding or resume.

```
oclGaussCrack64.bin < candidates.txt 1000000 2000000
```

Outcome: the first 1,000,000 input entries are skipped and the next 2,000,000 processed — the same candidate file can be sharded across several machines for distributed cracking, or resumed after an interruption by advancing the offset.

*No corpus evidence — derived from upstream documentation.*
