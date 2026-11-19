---
name: "ccrypt"
categories:
  - crypto-stego
version: "1.11-7"
homepage: "https://ccrypt.sourceforge.net/"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur, nixpkgs]
build-system: make
meta-checked: "2026-11-09"
github-repo: "wvangeit/ccrypt"
github-stars: 13
github-pushed: "2014-12-27"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ccrypt

> Rijndael-based encryption and decryption of files and streams.

## What it's for

ccrypt, written by Peter Selinger, encrypts and decrypts files and streams
with the Rijndael block cipher (the same cipher family used in AES), hashing
the user-supplied keyword to 256 bits. It is a traditional file encryptor,
not a steganography tool: data is transformed in place and marked with a
`.cpt` suffix. The interface is intentionally one-directional — you commit to
encrypting or decrypting — and ships convenience front ends (`ccencrypt`,
`ccdecrypt`, `ccat`) plus a compatibility mode for old Unix `crypt(1)` files.
It does not do key exchange, public-key crypto, or hiding data inside media.

## Typical use cases

### Encrypt a file in place

Prompt for a passphrase interactively and replace the file with its encrypted form.

```
ccencrypt secrets.txt
```

Outcome: prompts twice for a keyword, then writes `secrets.txt.cpt` and removes the plaintext file.

### Decrypt an encrypted file

Restore a `.cpt` file to its original content.

```
ccdecrypt secrets.txt.cpt
```

Outcome: after prompting for the correct keyword, recreates the original `secrets.txt` in the current directory.

### Decrypt straight to the terminal

Read encrypted content without writing a plaintext file to disk.

```
ccat secrets.txt.cpt
```

Outcome: prints the decrypted content to stdout; the `--cat` mode (`ccrypt -c`) is the same operation under the main binary.

### Supply the key non-interactively

Use ccrypt from scripts where an interactive prompt is impossible.

```
ccrypt -K 'Password1' -e notes.txt
```

Outcome: encrypts `notes.txt` to `notes.txt.cpt` using the keyword given with `-K` (the upstream documents this as unsafe — the key is visible in the process list; `-k keyfile` reads keywords from a file instead).

*No corpus evidence — derived from upstream documentation.*
