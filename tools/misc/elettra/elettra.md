---
name: "elettra"
categories:
  - blackarch-misc
version: "1.0"
homepage: "https://www.winstonsmith.info/julia/elettra/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-14"
build-system: cmake
license: custom
aliases: ["elettra-gui"]
meta-checked: "2026-11-09"
github-repo: "vecna/elettra"
github-stars: 2
github-pushed: "2016-02-07"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# elettra

> Plausible-deniability encryptor: packs several files, each with its own password, into one archive indistinguishable from random data.

## What it's for

Elettra (by the pseudonymous Julia, presented in a Phrack issue 65 article) implements plausible-deniable cryptography: an arbitrary number of files, each protected by its own password, are gzip-compressed and encrypted into a single archive that looks like random data. The archive records nothing about how many files it contains, so surrendering one password reveals exactly one file and there is no cryptographic way to prove any other file exists inside — the holder can plausibly claim the remaining bytes are padding. It is a small C CLI (mcrypt/mhash/zlib based, built with CMake) with five commands — `encrypt`, `decrypt`, `checkpass`, `help`, `example`; a wxWidgets GUI variant (`elettra-gui`) was released for Windows. The project dates from 2008 and is unmaintained, but the CLI still builds.

## Typical use cases

### Encrypt several files, one password each

Build a multi-file deniable archive.

```
elettra encrypt archive.bin 15% report.pdf::Password1 notes.txt::Password2
```

Outcome: compresses and encrypts report.pdf (opened with Password1) and notes.txt (Password2) into the single archive.bin, padded with 15% random data to obscure the payload size; passwords omitted from the command line are prompted interactively.

### Recover one file with its password

Open only the file whose password you hold.

```
elettra decrypt archive.bin Password1 ./out/
```

Outcome: decrypts and writes just the file bound to Password1 into ./out/, leaving the rest of the archive cryptographically invisible — the same run cannot even confirm another file exists.

### Validate a password combination before encrypting

Check that a planned password set is usable for the archive layout.

```
elettra checkpass Password1 Password2 Password3
```

Outcome: verifies the candidate password combination against the archive's password-block sizing and either reports that the combination works ("password(s) combinations work ok ... use it") or warns of a bad sequence — as `encrypt` itself does when given a rejected sequence.

*No corpus evidence — derived from upstream documentation.*
