---
name: "tchunt-ng"
categories:
  - blackarch-forensic
version: "208.b8cf7fc"
homepage: "https://github.com/antagon/TCHunt-ng"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "antagon/TCHunt-ng"
github-stars: 54
github-pushed: "2018-11-09"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tchunt-ng

> Detects encrypted files — TrueCrypt/VeraCrypt containers, LUKS, GPG data, private keys — by statistical analysis.

## What it's for

TCHunt-ng (a GPLv3 rewrite of Stephen Judge's TCHunt) flags files whose content is encrypted or otherwise high-entropy: TrueCrypt, VeraCrypt and CipherShed containers, LUKS, EncFS, PGP/GPG encrypted files, OpenSSH and PEM private keys, password databases, and files of random data. Each file is classified with a libmagic type check plus a chi-squared test. Known limits: it cannot distinguish genuinely encrypted content from any other random data, and files under 32 bytes are ignored unless recognized by type. The binary is `tchuntng` upstream; BlackArch packages it as `tchunt-ng`.

## Typical use cases

### Test specific files

Check individual suspects for encryption.

```
tchunt-ng vault.bin
```

Outcome: prints the file name if its content is likely encrypted. Exit status carries the verdict: 0 = likely encrypted, 2 = not encrypted, 1 = error — so it drops straight into shell scripts.

### Sweep a whole directory tree

Hunt containers across an acquired filesystem.

```
find /mnt/image -type f | tchunt-ng -
```

Outcome: reads file names from stdin (the trailing `-`) and tests each one, printing the encrypted/high-entropy candidates — the standard idiom for scanning an entire mounted image.

### Show why a file was flagged

Get the classification behind the verdict.

```
tchunt-ng -s vault.bin
```

Outcome: prints the file's classification (matched libmagic type or random-data verdict) in addition to flagging it, which helps triage true containers from compressed media.

### Classic TrueCrypt heuristic

Apply the original TCHunt size rules.

```
tchunt-ng -T -p ./case/*
```

Outcome: enables TCHunt compatibility mode, which additionally requires candidate files to be larger than 19 kiB and a multiple of 512 bytes in size — the traditional TrueCrypt-container signature; `-p` preserves each file's access time to keep the examination non-intrusive.

*No corpus evidence — derived from upstream documentation.*
