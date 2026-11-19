---
name: "hyperion-crypter"
categories:
  - blackarch-windows
version: "2.3.1"
homepage: "http://nullsecurity.net/tools/binary.html"
packaged-by: [blackarch]
blackarch-builddate: "2020-03-23"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# hyperion-crypter

> Runtime encrypter for 32/64-bit PE files that decrypts themselves in memory by AES key search.

## What it's for

Hyperion is the reference runtime PE-crypter from the paper "Hyperion: Implementation of a PE-Crypter" by Christian Ammann (phobosys.de), distributed by nullsecurity (release 2.3.1, 2020, BSD). It wraps an input executable in a loader container and encrypts the original with AES-128 using a short, randomly generated key; when the container runs, the loader brute-forces that key (a few seconds), decrypts the original PE in memory and transfers control to it — optionally writing a log file for debugging. In practice it is used to defeat static signature detection when testing AV and packer behavior, and as the canonical teaching example of a self-decrypting crypter. The AES key is intentionally tiny and searched at every start, so protection is against static analysis only, not reverse engineering. Kali packages the older 2.0 (32-bit-only) release of this same tool as `hyperion` — see the [[hyperion]] pointer note.

## Typical use cases

### Encrypt an executable

Wrap a payload in a self-decrypting container with default settings.

```
hyperion.exe payload.exe encrypted.exe
```

Outcome: writes `encrypted.exe`, a container PE that decrypts itself on startup (expect a pause of a few seconds while the AES key is searched) and then runs the original `payload.exe` code; static scanners see only the encrypted blob.

### Tune key length and keyspace

Trade startup delay against brute-force cost.

```
hyperion.exe -k 8 -s 6 payload.exe encrypted.exe
```

Outcome: encrypts with an 8-byte key (default 6) whose bytes range over 0–5 (default 0–3), growing the keyspace the loader must search at every start — longer keys slow startup but shrink an analyst's head start.

### Encrypt with verbose output and a runtime log

See what the crypter and the container are doing.

```
hyperion.exe -v -l payload.exe encrypted.exe
```

Outcome: `-v` prints verbose progress while encrypting, and `-l` makes the packed executable write a `log.txt` when it runs, showing the in-memory decryption steps — useful when testing whether a container executes correctly on a target platform.

*No corpus evidence — derived from upstream documentation.*
