---
name: "cupp"
categories:
  - blackarch-cracker
version: "87.b77f1ae"
homepage: "http://www.remote-exploit.org/?page_id=418"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-19"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Mebus/cupp"
github-stars: 6613
github-pushed: "2026-07-17"
github-archived: false
github-issues: 55
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# cupp

> Interactive profiler that builds a password wordlist for one specific person from their name, dates, relations, and interests.

## What it's for

The Common User Passwords Profiler generates target-specific wordlists for legal penetration tests and forensic work: it interviews you about the subject — first/last name, nickname, partner, pet, company, hobbies, birthdates, important years — then produces candidate passwords by concatenating those tokens and mutating them (years appended, leetspeak substitutions, common suffixes), with the mutation behavior tuned in the bundled `cupp.cfg`. Because people build passwords from exactly these elements, a few hundred targeted candidates often beat a multi-million-word generic list against a known user's account. It can also enrich an existing wordlist with the same mutations, download large reference wordlists, and extract default credentials from the Alecto database. Run from a source checkout it is `python3 cupp.py`; packaged installs expose the `cupp` command.

## Typical use cases

### Profile a person interactively

```
cupp -i
```

Outcome: walks through the question set about the target (names, dates, pet, employer, interests, extra words), offers optional leet and numeric mutations, and writes the generated candidate list to a `.txt` file named after the target in the current directory — feed it straight to a cracker such as hashcat or John the Ripper.

### Enrich an existing wordlist

```
cupp -w wordlist.txt
```

Outcome: asks the same profile questions, then rewrites wordlist.txt's entries through the mutation engine, appending profile-derived variants — useful when a generic dictionary almost worked against a personal account.

### Pull large reference wordlists

```
cupp -l
```

Outcome: shows a numbered menu of wordlist collections hosted in the project's repository and downloads the chosen ones, giving quick access to common lists for the cracking phase that follows profiling.

### Extract default credentials from Alecto

```
cupp -a
```

Outcome: parses the Alecto DB (the merged and cleaned Phenoelit and CIRT default-account databases) for the device or vendor of interest, yielding the username/password pairs to try before any custom wordlist work.

*No corpus evidence — derived from upstream documentation.*
