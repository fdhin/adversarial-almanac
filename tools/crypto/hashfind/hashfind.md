---
name: "hashfind"
categories:
  - blackarch-crypto
version: "8.e9a9a14"
homepage: "https://github.com/rurapenthe/hashfind"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "rurapenthe/hashfind"
github-stars: 19
github-pushed: "2015-02-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hashfind

> Regex-driven extractor that pulls password hashes, e-mail addresses, and credit-card numbers out of files, directory trees, or stdin.

## What it's for

hashfind searches single files or whole directory trees (directories automatically enable recursive mode) for roughly 38 recognizable credential formats — raw MD5/NTLM-shaped digests, MySQL, Joomla, vBulletin, phpBB3, WordPress, Drupal 7, Unix SHA-512 crypt, AIX, Cisco type 8/9, MSSQL 2000/2005, Django PBKDF2, LDAP `{SSHA}`, Mediawiki, plus plain e-mails and credit-card numbers — selected by a numeric mode flag. Where a hash identifier such as hashid classifies a digest you already have, hashfind digs candidate hashes out of bulk data: dumps, config files, logs, harvested text. It matches on structure only, so a raw-MD5 hit may also be another 32-hex format; verify candidates before cracking. The packaged script is Python 2 era (BlackArch installs it as `/usr/bin/hashfind` with a python2 shebang).

## Typical use cases

### Extract raw 32-hex hashes from a directory tree

```sh
hashfind -m 1 -i /mnt/loot -n
```

Outcome: recursively scans every file under `/mnt/loot` and prints each MD5/MD4/MD2/NTLM-shaped 32-hex-character match, one per line, ready to pipe into a cracker.

### Target a specific platform's format

```sh
hashfind -m 9 -i shadow-backup.txt -n
```

Outcome: prints only SHA-512 crypt (`$6$`-style) strings found in the file. Other frequently used modes: 3 Joomla, 5 phpBB3, 6 WordPress-MD5, 7 Drupal 7, 10 e-mails, 11 credit cards.

### Harvest e-mails and card numbers from a dump

```sh
hashfind -m 10 -i dump.txt -n
hashfind -m 11 -i dump.txt -o cards.txt
```

Outcome: mode 10 prints e-mail addresses to stdout; mode 11 writes credit-card-shaped numbers to `cards.txt`, one per line — `-o` saves results to a file, `-n` sends them to stdout and suppresses info messages.

### Filter a live stream

```sh
cat access.log | hashfind -m 3 -s
```

Outcome: reads stdin and prints Joomla-format hashes as they are matched, without touching any file.

*No corpus evidence — derived from upstream documentation.*
