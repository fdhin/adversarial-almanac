---
name: "sherlocked"
categories:
  - blackarch-packer
version: "1.f190c2b"
homepage: "https://github.com/elfmaster/sherlocked"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "elfmaster/sherlocked"
github-stars: 105
github-pushed: "2014-10-30"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sherlocked

> Script protector: packs any script into a self-decrypting, anti-debugged ELF x86_64 executable.

## What it's for

Sherlocked (v3, by elfmaster of Bitlackeys, 2014) is a "universal script packer": it takes a script in any language, XOR-encrypts it with a key you choose, and embeds it in a generated ELF x86_64 executable. At runtime the executable decrypts the script to a randomly named file in `/tmp`, hands it — plus any command line arguments — to the interpreter you specified, waits, and deletes the script again. Despite the packer category it does not pack native binaries; its input is always a script and its output is an ELF wrapper. Unless the `-r` option is used, the key is stored (encrypted) inside the binary so it runs unattended; with `-r` the runner must supply the key, at the cost of the script no longer accepting command line arguments. The package also installs two helper utilities from the same tree, `gen-shellcode` and `stripx`.

## Typical use cases

### Pack a script into a standalone protected executable

```
sherlocked payload.py payload.elf MyK3y python3
```

Outcome: writes `payload.elf`, an x86_64 ELF containing the XOR-encrypted script with the key embedded. Running `./payload.elf arg1` decrypts the script to a random `/tmp` file, invokes `python3` on it with `arg1`, then deletes the decrypted copy.

### Pack a script that requires a key at run time

```
sherlocked payload.py locked.elf MyK3y python3 -r
```

Outcome: writes `locked.elf`, which refuses to run unless invoked as `./locked.elf MyK3y` — the key is not usable without being typed at execution. Note the upstream restriction: `-r`-protected executables take no command line arguments for the script, because arguments and key share one command line.

*No corpus evidence — derived from upstream documentation.*
