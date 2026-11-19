---
name: "pwdlogy"
categories:
  - blackarch-misc
version: "14.8b92bcf"
homepage: "https://github.com/tch1001/pwdlogy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "tch1001/pwdlogy"
github-stars: 62
github-pushed: "2016-11-24"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwdlogy

> Interactive Python console that builds target-specific password wordlists from a person's keywords, names and birthdays.

## What it's for

Targeted password guessing fails with generic wordlists because they ignore the victim's specifics. pwdlogy ("Powerful Target Specific Passwords Profiler", by tch1001) is an interactive console for building social-engineering wordlists: you supply the target's interests, favourite food, games, close names and birthdays, and it combines them with special characters, numbers and l33t substitutions according to configurable "formats" (kk = keyword+keyword, kb = keyword+simple birthday, and so on). Input arrives through two plain text files — `keywords.txt` (one keyword per line) and `birthday.txt` (dates as DDMMYYYY) — and the generated list lands in `gen.txt`, ready for any password cracker. It is a generator only: no network activity, no cracking. The BlackArch package wraps it as the `pwdlogy` command launching the Python 3 console.

## Typical use cases

### Generate a basic targeted wordlist

```
pwdlogy
start
```

Outcome: `pwdlogy` opens the `pwdlogy >` console; after keywords.txt and birthday.txt are filled in (in the tool's directory under /usr/share/pwdlogy), `start` combines them through the default format list and writes the finished wordlist to gen.txt.

### Point the generator at your own input and output files

```
set keywordsFile my-keywords.txt
set genDictName target-wordlist.txt
start
```

Outcome: reads keywords from my-keywords.txt for this session and generates to target-wordlist.txt instead of the defaults — settings changed with `set` last for the current session only.

### Save and reload a target's settings

```
save target-settings.conf
load target-settings.conf
```

Outcome: writes the current settings (keyword files, charsets, birthday usage, fast mode) to target-settings.conf and restores them in a later session, so a target's profile does not have to be re-entered.

### Customize the password formats

```
custom my-formats.conf
set formatsFile my-formats.conf
start
```

Outcome: `custom` writes a copy of the default format list to my-formats.conf for you to extend with your own specifiers; pointing formatsFile at it makes `start` generate with your formats.

*No corpus evidence — derived from upstream documentation.*
