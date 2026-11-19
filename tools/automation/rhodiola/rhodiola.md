---
name: "rhodiola"
categories:
  - blackarch-automation
version: "4.8bc08a0"
homepage: "https://github.com/utkusen/rhodiola"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "utkusen/rhodiola"
github-stars: 136
github-pushed: "2019-12-21"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rhodiola

> Targeted wordlist generator ("crunch2049") that mines a person's tweets for the nouns, hobbies, places and years their passwords are actually built from.

## What it's for

Shrinking a password-guessing candidate pool by building a wordlist around one specific person instead of a generic dictionary. Rhodiola analyzes a Twitter account's posts, extracts the most-used nouns and proper nouns, pairs them, and combines them with related cities and years — matching the observed habit of people coining passwords from their interests. It then mangles those seeds three ways: plain, by appending/prefixing strings generated from a regex you supply, and by hashcat-style case masks (`?u`/`?l` only). If you have no Twitter API keys (or none anymore), it accepts raw text files or URL lists instead. It does not attack anything itself and generates no rules or masks beyond its own — its output is a plain wordlist intended for feeding into hashcat, John or an online login test. Note: upstream is a Python 2.7 project archived since 2019, and the Twitter API it depends on is no longer free, so the tweet-mining mode is effectively historical; the bring-your-own-data modes remain usable.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Build a base wordlist from a target's tweets

```
python rhodiola.py --username target_handle
```

Outcome: pulls the account's tweets, extracts nouns and proper nouns, and prints a personalized candidate list — interests as words (`spacex`, `rocket`), interest+year compounds (`boringcompany2018`), city and pairing variants. Redirect to a file (`> target.wordlist`) and feed it to your cracker or spraying tool.

### Extend the seed words with regex-generated suffixes

```
python rhodiola.py --username target_handle --regex "(root|admin)\d{2}"
```

Outcome: every string matching the regex is generated and appended (default; use `--regex_place prefix` to prepend instead) to each mined word, producing candidates like `teslaroot01` and `spacexadmin99` in addition to the base list.

### Case-mangle the output with hashcat-style masks

```
python rhodiola.py --username target_handle --mask "?u?l?u?u?l"
```

Outcome: applies the mask's upper/lower-alpha pattern to each word, yielding mixed-case variants (`TeSLa`, `RoCKet`, `TeSLa1856`) that cover common capitalization habits. Only `\l` (lower) and `\u` (upper) charsets are supported.

### Use your own text or URLs instead of Twitter

```
python rhodiola.py --filename notes_about_target.txt
```

Outcome: runs the same noun/compound/year analysis over arbitrary text you supply (notes, profiles, biographies), producing the same style of personalized wordlist without any Twitter API access. The sibling mode `python rhodiola.py --urlfile blogs.txt` scrapes the listed URLs first and then does the same over their text.
