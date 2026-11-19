---
name: "hashid"
categories:
  - blackarch-crypto
version: "397.7e8473a"
homepage: "https://github.com/psypanda/hashID"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "psypanda/hashID"
github-stars: 1476
github-pushed: "2022-06-20"
github-archived: false
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hashid

> Identify 220+ hash and password-storage formats from a digest, with the matching hashcat mode and John the Ripper format.

## What it's for

hashID (installed as `hashid`, from `hashid.py`) names the candidate formats of a hash using regular-expression prototypes — bcrypt, WordPress `$P$`, MySQL 4.1/5.x, Blowfish (OpenBSD), PBKDF2, RACF, raw MD5/SHA families, and hundreds more. It is the standard first step before cracking: given a digest recovered from a database dump or config, it tells you which `hashcat -m` mode and which John `--format` to use. It ranks candidates rather than proving one — a bare 32-hex string is reported as every 32-hex format (MD5 listed first) since structure alone cannot distinguish them. It accepts a single hash on the command line (quote it on *nix to prevent shell interpolation), a file, or a directory of files, and can emit the analysis to an outfile.

## Typical use cases

### Identify a single hash

```sh
hashid '$2a$08$VPzNKPAY60FsAbnq.c.h5.XTCZtC1z.j3hnlDFGImN9FcpfR1QnLq'
```

Outcome: prints `Analyzing '<hash>'` followed by candidate formats in order, bcrypt first — confirming the digest family before any cracking attempt. The single quotes stop the shell from expanding `$` fields in the hash.

### Get the hashcat mode and John format directly

```sh
hashid -mj '$racf$*AAAAAAAA*3c44ee7f409c9a9b'
```

Outcome: each candidate gains `[Hashcat Mode: 8500][JtR Format: racf]`-style annotations (`-m` adds the hashcat mode, `-j` the John format), giving the exact flags for the follow-up cracking command.

### Triage a file of mixed-format hashes

```sh
hashid hashes.txt
```

Outcome: walks the file line by line, printing `--File 'hashes.txt'--`, then each analyzed hash with its candidate list, then `--End of file--` — one pass classifies a whole dumped credential file. Directories are processed the same way.

### Include salted and extended variants

```sh
hashid -e '4628b9b168ef075609dbc91b6284e286'
```

Outcome: in addition to the usual 32-hex candidates (MD5 first), lists salted constructions such as `md5($pass.$salt)` style variants — useful when the target system is known to salt.

### Save the analysis for later

```sh
hashid -m -o results.txt hashes.txt
```

Outcome: writes the hashcat-mode-annotated analysis of every hash in the file to `results.txt` instead of stdout, keeping the terminal clean for large files.
