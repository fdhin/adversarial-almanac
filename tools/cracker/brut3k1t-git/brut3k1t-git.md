---
name: "brut3k1t-git"
categories:
  - cracker
version: "20210609.r104"
homepage: "https://github.com/ex0dus-0x/brute"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ex0dus-0x/brute"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# brut3k1t-git

> Plugin-based Python credential-stuffing and brute-force engine (upstream name "brute"); the package name survives its earlier life as brut3k1t.

## What it's for

The ArchStrike package brut3k1t-git builds the final upstream project, ex0dus-0x/brute — since deleted from GitHub, and documented here from its archived README — a Python 3 CLI and library for brute-force and credential-stuffing attacks against network protocols and web logins. Attacks are expressed as modules: seven ship by default and you can generate, run standalone, or register your own plugins. Input is flexible: a single username, a comma-separated or file-based user list, a wordlist from a local path or a URL, or a combo list of `user:pass` pairs. The 2017-era brut3k1t CLI (`brut3k1t.py` with `-s <service>`) no longer exists in this codebase; the current entry point is `brute`.

## Typical use cases

### See the available attack modules

```
brute --list_modules
```

Outcome: prints every module in the local registry, each of which can be launched with `-m`; default modules cover common network protocols and web services.

### Run a module against a user list and a wordlist URL

```
brute -m mysite -u alice,bob, --wordlist https://example.com/leak.txt -t 5
```

Outcome: tries every password for each named user through the mysite module, moving on after the 5-second per-user timeout, and reports any valid credentials found.

### Attack from a combo list

```
brute -m mysite -c combos.txt
```

Outcome: reads `user:pass` pairs line by line and tests each pair directly (username and wordlist flags are ignored); combos.txt may be a local path or an HTTP URL.

### Scaffold a new attack module

```
brute --new_module web/mysite
```

Outcome: writes a template plugin script (mysite.py) implementing the library's brute-force interface; edit it, run it standalone (`python3 mysite.py --username user --wordlist wordlist.txt`), or register it with `brute --add_module mysite.py`.

*No corpus evidence — derived from upstream documentation.*
