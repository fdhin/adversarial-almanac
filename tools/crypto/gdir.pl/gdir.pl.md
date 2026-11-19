---
name: "gdir.pl"
categories:
  - blackarch-crypto
version: "9.d2cc05d"
homepage: "https://gitlab.com/GasparVardanyan/gdir.pl"
packaged-by: [blackarch]
blackarch-builddate: "2025-09-27"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# gdir.pl

> Perl wrapper that drives gcrypt over whole directory trees — encrypt, decrypt, list, read, and rename inside an encrypted directory.

## What it's for

gcrypt itself handles one file at a time; gdir.pl applies it recursively to directories while keeping the stored tree usable in encrypted form: you can list its files, cat a single member out in place, and rename entries, all with the same key operand. It is the companion tool the gcrypt README points to for directory encryption. As with gcrypt, the key is a command-line argument, so it is visible in shell history and process listings.

## Typical use cases

### Encrypt a directory

```
gdir.pl -e Password1 ./documents ./documents.enc
```

Outcome: walks the source directory and writes its encrypted form under `./documents.enc`.

### Decrypt a directory

```
gdir.pl -d Password1 ./documents.enc ./documents.out
```

Outcome: restores the plaintext tree into `./documents.out`.

### List the contents of an encrypted directory

```
gdir.pl -l Password1 ./documents.enc
```

Outcome: prints the file listing of the encrypted directory without decrypting anything.

### Read one file without decrypting the tree

```
gdir.pl -c Password1 ./documents.enc notes.txt
```

Outcome: decrypts only the named member file and writes it to stdout (cat semantics), leaving the rest of the encrypted tree untouched.

### Rename files inside the encrypted directory

```
gdir.pl -R Password1 ./documents.enc
```

Outcome: renames files stored in the encrypted tree in place, keeping it consistent under the same key.

*No corpus evidence — derived from upstream documentation.*
