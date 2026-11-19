---
name: "c7decrypt"
categories:
  - blackarch-crypto
version: "0.3.3"
homepage: "https://github.com/claudijd/c7decrypt"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "claudijd/c7decrypt"
github-stars: 29
github-pushed: "2023-07-11"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# c7decrypt

> Cisco Type-7 password decryptor and encryptor — reverses the weak obfuscation Cisco devices apply to enabled passwords, key strings, and SNMP community strings.

## What it's for

Cisco routers and switches "encrypt" various passwords with the reversible Type-7 scheme (the `7` in `password 7 04480E...`), which is a Vigenère-style XOR with a fixed, publicly known key — obfuscation, not encryption. c7decrypt is a Ruby implementation that turns those strings back into plaintext, both as a command-line tool and as a library (`require 'c7decrypt'`) for scripting. It also encrypts Type-7 (with control over the seed byte, useful for crafting configs) and can produce Type-5 (MD5-based) hashes, though Type-5 cannot be reversed — only cracked offline. Type-7 strings found in configs, backups, and `show running-config` output are all fair game.

## Typical use cases

### Decrypt a single Type-7 string

```
c7decrypt -s 04480E051A33490E
```

Outcome: prints the recovered plaintext for the given hash (`secure` for this classic example) — instant, since the key is fixed. Use this when you have one string off a config line such as `enable password 7 04480E051A33490E` and want the cleartext password behind it.

### Decrypt every Type-7 hash in a config file

```
c7decrypt -f config.txt
```

Outcome: scans `config.txt` and prints the decrypted plaintext for each Type-7 hash found — password lines, Wi-Fi pre-shared keys, SNMP communities, and routing key strings in one pass. This is the batch form to run over an extracted `running-config` or a directory-dumped config bundle.

### Encrypt or decrypt from Ruby

```
ruby -e 'require "c7decrypt"; puts C7Decrypt::Type7.decrypt("060506324F41"); puts C7Decrypt::Type7.encrypt("cisco")'
```

Outcome: prints `cisco` (the decryption) followed by a Type-7 encoding of `cisco` (`02050D480809`). This library interface is what makes c7decrypt useful inside larger automation — mass config parsing, report generation, or generating Type-7 values for lab configs — and `C7Decrypt::Type5.encrypt("cisco")` produces a UNIX-MD5 Type-5 hash for the same config format.

*No corpus evidence — derived from upstream documentation.*
