---
name: "pwd-hash"
categories:
  - blackarch-crypto
version: "2.0"
homepage: "http://vladz.devzero.fr/pwd-hash.php"
packaged-by: [blackarch]
blackarch-builddate: "2022-08-29"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pwd-hash

> Generates crypt(3)-style password hashes (DES, MD5, SHA-512, GRUB) from a password read on stdin.

## What it's for

pwd-hash is a small C utility that answers a recurring question when hardening Unix systems: what will a password look like to `crypt(3)` under a given scheme? You pipe or type a password, choose a scheme, and it prints the resulting hash with a random or chosen salt — handy for producing entries for `/etc/shadow`, GRUB passwords, or checking how a scheme behaves. Supported schemes are the traditional DES crypt, the MD5-based scheme used by GRUB (6-byte salt), the regular MD5 scheme (8-byte salt, the default), and SHA-512 (`--sha`). It only generates hashes; it does not crack or verify passwords.

## Typical use cases

### Hash a password with the default MD5 scheme

```
echo "P4ssw0rD" | pwd-hash --md5
```

Outcome: prints an MD5-crypt hash with a random 8-byte salt on stdout, in the `$1$salt$hash` form (e.g. `$1$xz8FmRfw$n0/NbiSlDlVagK6eSZHpO1`); `--md5` is the default when no scheme is given.

### Generate a GRUB password hash

```
pwd-hash --grub
```

Outcome: prompts for the password interactively and prints the MD5-crypt hash with the 6-byte salt GRUB expects (e.g. `$1$8Zr3Uz$Wj5RwlM831qYjKpaRijh0.`).

### Hash interactively without echoing the password

```
pwd-hash -n --sha
```

Outcome: reads the password from the terminal without echoing the characters (`-n`/`--noecho`) and prints the SHA-512 crypt hash (`$6$...` form).

### Reproduce a hash with a fixed salt

```
echo "p4ssw0rd" | pwd-hash --salt "AL" --des
```

Outcome: prints the deterministic traditional-DES hash (`ALAInZSoRAjso` for this exact pair) — a fixed `--salt` removes the randomness so the same password always yields the same hash; each scheme enforces its own salt length (2 characters for DES, 6 for GRUB, 8 for MD5) and the tool exits with an error if it does not match.

*No corpus evidence — derived from upstream documentation.*
