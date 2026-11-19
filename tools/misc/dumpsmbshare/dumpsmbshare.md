---
name: "dumpsmbshare"
categories:
  - blackarch-misc
version: "23.1e5ceb1"
homepage: "https://github.com/p0dalirius/DumpSMBShare"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "p0dalirius/DumpSMBShare"
github-stars: 232
github-pushed: "2025-01-31"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dumpsmbshare

> Impacket-based script that lists and dumps files from a remote Windows SMB share without mounting it.

## What it's for

DumpSMBShare (Podalirius) is a Python script, built on Impacket's SMB layer, that walks a remote Windows share and copies its contents to a local directory — without mounting the share or touching SMB client tooling on the host. Authentication supports password, NTLM hash pass-the-hash (`-H`), and Kerberos (`-k`, optionally with `-A` AES key); the dump can be scoped with `--extensions` filters, a `--base-dir` search root inside the share, or a single `--file`. Typical use is pulling interesting share content (SYSVOL scripts, configs, backups) during an assessment, either whole or filtered by extension. It only reads from the remote share.

## Typical use cases

### Enumerate shares on a host

Discover what the account can see before dumping anything.

```
dumpsmbshare -l 'domain.local/user:Password1@198.51.100.10'
```

Outcome: authenticates over SMB and prints the list of shares available on the remote machine; add `--debug` to see the underlying Impacket traffic.

### List the files inside one share

Preview a share's contents without downloading.

```
dumpsmbshare -s 'SYSVOL' -L 'domain.local/user:Password1@198.51.100.10'
```

Outcome: walks the share recursively and prints its full remote file listing; nothing is written locally.

### Dump a share filtered by extension

Pull only the file types that matter.

```
dumpsmbshare -s 'SYSVOL' -e bat,ps1,xml -D loot 'domain.local/user:Password1@198.51.100.10'
```

Outcome: downloads every `.bat`, `.ps1` and `.xml` file found from the share root (`-B` narrows the search base directory) into the local `loot/` directory, keeping the share's directory structure.

### Dump using pass-the-hash

Use a captured NTLM hash instead of a password.

```
dumpsmbshare -s 'SYSVOL' -H 'LMHASH:NTHASH' 'domain.local/user@198.51.100.10'
```

Outcome: authenticates with the supplied LM:NT hash pair exactly as a password login would and dumps the share as above; Kerberos environments can instead use `-k --no-pass` with `$KRB5CCNAME` (and `-A` for the AES key), resolving the target with `-I` when names don't resolve.

*No corpus evidence — derived from upstream documentation.*
