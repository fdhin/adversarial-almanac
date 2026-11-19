---
name: "bruteforce-salted-openssl"
categories:
  - blackarch-cracker
version: "64.30b272e"
homepage: "https://github.com/glv2/bruteforce-salted-openssl"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-03-02"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "glv2/bruteforce-salted-openssl"
github-stars: 238
github-pushed: "2024-04-30"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bruteforce-salted-openssl

> Recover the password of a file encrypted with `openssl enc -salt` by dictionary or exhaustive search.

## What it's for

Files encrypted with commands like `openssl enc -aes256 -salt -in clear.file -out encrypted.file` carry no header recording the cipher, digest or key derivation, so ordinary hash crackers cannot fingerprint them. bruteforce-salted-openssl re-derives the key and IV exactly as OpenSSL does — for any cipher and digest the installed OpenSSL libraries support — and tests candidate passwords, treating a decryption as successful when the plaintext is at least 90% printable ASCII, or when it starts with a caller-supplied magic string. Dictionary and exhaustive modes, multithreaded, resumable via a state file. Text-centric detection means binary plaintext needs the `-M` option, and files encrypted with OpenSSL's PBKDF2 mode need `-K` with the matching iteration count.

## Typical use cases

### List the available algorithms

See which ciphers and digests can be passed to `-c` and `-d` on this system.

```
bruteforce-salted-openssl -a
```

Outcome: prints every cipher and digest name supported by the installed OpenSSL libraries; required because the encryption command's arguments are not stored in the encrypted file and must be guessed or known.

### Crack a file with a wordlist

Replay a password file against an encrypted file for a known cipher/digest pair.

```
bruteforce-salted-openssl -t 4 -f passwords.txt -c aes-256-cbc -d sha256 encrypted.file
```

Outcome: four threads derive keys for each wordlist entry and decrypt the file; a hit prints `Password candidate: <password>` on stdout (heuristic validation — verify by decrypting), and an exhausted list prints `Password not found.`

### Exhaustive search around a partially remembered password

Generate candidates with known beginning and end from a restricted character set.

```
bruteforce-salted-openssl -t 8 -l 9 -m 11 -c des3 -b "AbCD" -e "Ef" \
  -s "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz" encrypted.file
```

Outcome: eight threads try every 9-to-11-character string starting with `AbCD`, ending with `Ef`, letters only, against the des3-encrypted file, printing any password candidate.

### Match binary plaintext by magic bytes

Recognize correct decryption of non-text data such as gzip.

```
bruteforce-salted-openssl -t 8 -v 5 -c des3 -m 9 -s "0123456789" -M "$(echo -ne '\x1f\x8b')" archive.tar.gz.des3
```

Outcome: instead of the printable-ASCII heuristic, a decryption counts as successful only when it begins with the gzip magic bytes `1f 8b`, making false positives on binary files unlikely.

### Attack a PBKDF2-encrypted file

Handle modern `openssl enc -pbkdf2` output.

```
bruteforce-salted-openssl -t 4 -f passwords.txt -c aes-256-cbc -d sha256 -K -i 10000 encrypted.file
```

Outcome: keys are derived with PBKDF2 (`-K`) at the same iteration count used for encryption (`-i`, OpenSSL's default is 10000) instead of the legacy single-shot digest, so PBKDF2 files validate correctly.

### Decrypt the file once a password is found

Use OpenSSL itself to verify and recover the plaintext.

```
openssl enc -d -aes256 -in encrypted.file -out decrypted.file -k '<recovered-password>'
```

Outcome: writes the full decrypted plaintext to decrypted.file, confirming the candidate password found by the search.
