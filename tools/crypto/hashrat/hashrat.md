---
name: "hashrat"
categories:
  - blackarch-crypto
version: "1.15"
homepage: "https://github.com/ColumPaget/Hashrat"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2023-10-19"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ColumPaget/Hashrat"
github-stars: 75
github-pushed: "2025-09-11"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hashrat

> Hashing Swiss-army knife: generate, verify and hunt by hashes — files, stdin, whole filesystems, even remote targets over SSH or HTTP.

## What it's for

hashrat (Colum Paget) does digest work far beyond `md5sum`: hashes stdin, files or entire directory trees with MD5, SHA-1, SHA-256, SHA-384, SHA-512, Whirlpool and JH (plus any OpenSSL algorithm, chainable like `-type sha512,md5`); verifies manifests in md5sum-style or its extended format; searches a filesystem for files matching a list of hashes (IoC-style hunts, with memcached support for huge lists); finds duplicate files; hashes remote files via SSH or HTTP URLs; caches results in xattrs; and computes HMACs and TOTP codes. It does not crack or identify hashes — it only computes and matches them.

## Typical use cases

### Hash data from stdin

Quick digest of a string or stream, in any supported algorithm and encoding.

```
echo -n secretdata | hashrat -sha256 -64
```

Outcome: prints the SHA-256 of stdin encoded in base64 (drop `-64` for hex); `-lines` hashes each line separately, `-trad` matches `md5sum`-compatible output.

### Hash a directory tree recursively into a manifest

Produce a checksum list for a whole subtree.

```
hashrat -r -sha1 /srv/www > hashes.sha1
```

Outcome: one `sha1sum`-style line per file under `/srv/www`, written to `hashes.sha1` — usable later by hashrat or any md5sum-family tool.

### Verify a manifest and report only failures

Re-check integrity and surface just what changed.

```
cat hashes.sha1 | hashrat -cf
```

Outcome: each listed file is rehashed and compared; only mismatches (and errors) are printed — exit with the list of drifted files; `-c -strict` additionally flags uid/gid/size/mtime/inode changes.

### Hunt a filesystem for files matching known hashes

Sweep for files by digest, the classic IoC-search pattern.

```
cat indicators.md5 | hashrat -m -r /
```

Outcome: hashrat reads the hash list from stdin, walks `/` recursively, and prints the paths of files whose MD5 matches an indicator; with `-lm -memcached 127.0.0.1` the list is registered in memcached first for repeated large-scale searches.

### Find duplicate files

Locate space-wasting duplicates under a directory.

```
hashrat -dups -r /home/user -u xattr
```

Outcome: groups of files with identical digests are reported; `-u xattr` stores computed hashes in filesystem extended attributes so later runs skip rehashing unchanged files.

### Hash a remote file over SSH or HTTP

Digest files you cannot (or need not) copy locally.

```
hashrat -sha1 -net ssh:user:Password1@target.example.com/bin/ls
hashrat -sha256 -net http://target.example.com/index.html
```

Outcome: the remote file is fetched and hashed in transit and its digest prints locally without leaving a copy on disk.

*No corpus evidence — derived from upstream documentation.*
