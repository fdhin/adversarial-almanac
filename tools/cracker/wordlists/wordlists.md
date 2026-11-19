---
name: "wordlists"
categories:
  - passwords
version: "2026.2.0"
homepage: "https://www.kali.org"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur, nixpkgs]
build-system: scripts
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# wordlists

> Kali's central wordlist package: ships the rockyou password list and keeps `/usr/share/wordlists/` as one directory of wordlists contributed by other tools.

*No corpus evidence — derived from upstream documentation.*

## What it's for

The Kali `wordlists` package turns `/usr/share/wordlists/` into the
distribution's single wordlist location. It installs `rockyou.txt.gz`
(about 134 MB installed) and maintains a farm of symlinks pointing at
wordlists owned by other packages — seclists, metasploit, dirb, dirbuster,
john, nmap, wfuzz, amass, and more — so every cracking tool can be pointed
at one path. It also installs a small `wordlists` helper command that lists
what is available and interactively decompresses rockyou. The package has
no cracking logic of its own; it exists so the lists are in a known place.

## Typical use cases

### Survey and extract the available wordlists

Interactively list everything under the central directory and unpack rockyou.

```
wordlists
```

Outcome: prints a tree of `/usr/share/wordlists/` (rockyou plus the
per-tool symlink farm), then — if `rockyou.txt` is not yet present — asks
`Do you want to extract the wordlist rockyou.txt? [Y/n]` and runs
`gunzip -k rockyou.txt.gz` on confirmation, finally dropping you into a
shell already cd'd to `/usr/share/wordlists`.

### Decompress rockyou without the helper

Unpack the shipped archive non-interactively, e.g. in scripts.

```
sudo gunzip -k /usr/share/wordlists/rockyou.txt.gz
wc -l /usr/share/wordlists/rockyou.txt
```

Outcome: creates the plain-text `rockyou.txt` next to the kept `.gz`
original (roughly 14 million candidate passwords, one per line); `wc -l`
reports the number of candidates for sizing a planned attack.

### Filter candidates to a password policy

Trim the list to entries that satisfy a minimum-length rule before feeding
a slow cracker or an online form with validation.

```
grep -E '^.{10,}$' /usr/share/wordlists/rockyou.txt > rockyou-10plus.txt
```

Outcome: writes `rockyou-10plus.txt` containing only candidates of ten
characters or more; pass that file instead of the full list via any
tool's `--wordlist=`/`-w` option to cut run time.

### Locate another package's wordlists

List the symlink farm to find where a given tool's lists live.

```
ls -l /usr/share/wordlists/
```

Outcome: shows entries such as `seclists -> /usr/share/seclists`,
`metasploit -> /usr/share/metasploit-framework/data/wordlists` and
`john.lst -> /usr/share/john/password.lst`, each usable directly as a
wordlist path or as a starting point for browsing subdirectories.
