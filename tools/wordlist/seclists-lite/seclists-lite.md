---
name: "seclists-lite"
categories:
  - wordlist
version: "2024.4-0parrot1"
homepage: "https://www.owasp.org/index.php/Projects/OWASP_SecLists_Project"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# seclists-lite

> Parrot's minimal packaging of the SecLists dataset — a trimmed subset of the same upstream lists.

## What it's for

`seclists-lite` is not a separate project. It is Parrot Security's Debian package shipping a deliberately small subset of the OWASP SecLists collection (its own package description: "a small subset of the larger SecLists project") for installs that do not want the full, multi-gigabyte dataset. The upstream is the same repository (danielmiessler/SecLists), the file naming and directory layout follow it, and the way the lists are consumed is identical — fuzzers and brute-forcers read them by path.

See [[seclists]] for the full reference: the directory taxonomy (`Discovery/`, `Fuzzing/`, `Passwords/`, `Usernames/`, `Web-Shells/`, ...) and the consumer command patterns (ffuf, gobuster, wfuzz, hydra, feroxbuster) all apply unchanged to the files this package installs.

*No corpus evidence — derived from upstream documentation.*
