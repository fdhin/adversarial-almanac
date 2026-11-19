---
name: "morxcrack"
categories:
  - blackarch-cracker
version: "1.2"
homepage: "http://www.morxploit.com/morxcrack/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-06"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morxcrack

> Offline Perl dictionary cracker for plain, salted, and CMS-formatted password hashes.

## What it's for

MorXCrack runs a dictionary attack against a single hash given on the command line. Supported formats: MD5, SHA1 and SHA2 (plain, hashed twice, and salted as password+salt or salt+password), MySQL 4.1+ password hashes, and Unix `crypt` shadow hashes, plus the CMS formats Joomla (md5 of password+salt), vBulletin and InvisionPowerBoard — which take the salt as a fourth argument — and WordPress PHPass, which needs no salt (single-quote hashes containing `$`). It is pure Perl on Digest::MD5/Digest::SHA, reports the found password with its wordlist line number and elapsed time, and processes one hash per run; there is no GPU, rule, or mask support. The author's site has expired, so the packaged script and mirrored documentation are the reference.

## Typical use cases

### Crack a plain MD5 hash

Point it at an unsalted hash and a wordlist.

```
morxcrack md5 83583d2b5ea4078b9b83f82254e5d564 wordlist.txt
```

Outcome: prints the target hash and algorithm, `[+] Cracking ...`, and on a hit `[+] Your password is morxploit` with the wordlist line number and elapsed seconds — the demo hash is the MD5 of `morxploit`.

### Crack a salted Joomla password hash

Supply hash and salt as separate arguments.

```
morxcrack joomla a87248e5fc69972804f5bb93c873ee9d wordlist.txt 9W11uZafPxbe9xpL
```

Outcome: hashes each candidate as md5(password+salt); the demo pair is the password `morxploit` with salt `9W11uZafPxbe9xpL`, and the found-password report matches the unsalted case. The `vb` (vBulletin) and `ipb` (InvisionPowerBoard) formats take the same four-argument form.

### Crack a MySQL 4.1+ password hash

Attack the starred 41-character format used since MySQL 4.1.

```
morxcrack mysql '*7FC0400E6672126FFD2EE3BFF80830271E997A7F' wordlist.txt
```

Outcome: tests each wordlist entry against sha1(sha1(password)) and reports the plaintext on a match; the demo hash is the MySQL 4.1+ hash of `morxploit`.

*No corpus evidence — derived from upstream documentation.*
