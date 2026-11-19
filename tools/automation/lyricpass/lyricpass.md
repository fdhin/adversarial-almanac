---
name: "lyricpass"
categories:
  - blackarch-automation
version: "45.fd31d07"
homepage: "https://github.com/initstring/lyricpass"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "initstring/lyricpass"
github-stars: 102
github-pushed: "2024-02-24"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lyricpass

> Builds passphrase-cracking wordlists out of song lyrics.

## What it's for

lyricpass scrapes song lyrics for one artist or a list of artists and turns them into offline-cracking wordlists: `raw-lyrics.txt` with all lyric lines and `wordlist.txt` with likely passphrase candidates, length-filtered. It targets password policies that push users toward long "memorable" passphrases that are often lyric lines. Best combined with rule-based cracking in hashcat or John the Ripper (the author's passphrase-wordlist project pairs well). The upstream repository is archived (read-only since 2024). It does not crack anything itself — it only generates candidate lists.

## Typical use cases

### Scrape lyrics for a single artist

Generate candidates from one band or musician.

```
./lyricpass.py -a "Rob Zombie"
```

Outcome: fetches all songs' lyrics it can find for the artist and writes `raw-lyrics.txt` (every lyric line) plus `wordlist.txt` (passphrase candidates) in the working directory.

### Process a list of artists with length constraints

Work through many artists and pre-filter by policy length.

```
./lyricpass.py -i artists.txt --min 10 --max 32
```

Outcome: iterates the file (one artist per line), producing the same two outputs and keeping only candidates between 10 and 32 characters — defaults are 8 to 40, so narrow the range to match the target password policy.

### Crack with the lyric wordlist plus rules

Feed the candidates to hashcat with mangling rules.

```
hashcat -m 22000 capture.hc22000 wordlist.txt -r rules/best64.rule
```

Outcome: hashcat tries each lyric phrase, then rule-mangled variants — lyric passphrases with appended digits or leetspeak survive the wordlist while the rules cover the common mutations.

*No corpus evidence — derived from upstream documentation.*
