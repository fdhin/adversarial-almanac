---
name: "cewl"
categories:
  - blackarch-automation
version: "6.2.1.r33.gca01aea"
homepage: "http://www.digininja.org/projects/cewl.php"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-06-29"
build-system: scripts
license: CC-BY-NC-SA-2.0, GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "digininja/CeWL"
github-stars: 2877
github-pushed: "2026-06-24"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 21
gh-activity-checked: "2026-11-09"
---

# cewl

> Spiders a website and builds a custom wordlist from its own words, emails, and document metadata.

## What it's for

CeWL (pronounced "cool") is a Ruby app that spiders a given URL to a chosen depth and returns the unique words it finds — the vocabulary the target organization actually writes — making it the standard way to build target-specific password lists and content-discovery dictionaries before credential attacks or web fuzzing. By default it stays on the site you specify, spiders to depth 2, and prints all words of three or more characters; flags control spider depth, word length bounds, case handling, digits in words, and whether email addresses and metadata from documents found on the site are collected too. Words go to stdout, so output redirects cleanly into files or other tools. It does no cracking or fuzzing itself — pair the resulting list with John/hashcat for passwords or ffuf/dirb-style tools for content discovery. Be careful combining a large `-d` depth with `-o` (follow offsite links): the spider can drift onto unrelated domains. The bundled companion FAB (Files Already Bagged) applies the same metadata-extraction techniques to files you already downloaded.

## Typical use cases

### Build a password wordlist from the site's own text

The baseline pattern: harvest the target's own vocabulary as a password list.

```
cewl http://target.example.com > wordlist.txt
```

Outcome: spiders the site to depth 2 and writes every unique word of 3+ characters to `wordlist.txt`, ready to feed to a hash cracker or spraying tool.

### Normalize case for content discovery

Directory and file names are usually lowercase; normalize before fuzzing.

```
cewl http://target.example.com | tr '[:upper:]' '[:lower:]' > wordlist.txt
```

Outcome: same spider run with every word lowercased in the pipe, producing a list suitable for case-sensitive web fuzzing (`wc -l` on the file shows its size); recent CeWL versions can do this natively with `--lowercase`.

### Spider deeper and write straight to a file

Longer words, more pages, no shell redirect.

```
cewl -d 3 -m 5 -w cewl.txt http://target.example.com
```

Outcome: follows links to depth 3, keeps only words of at least 5 characters, and writes the list directly to `cewl.txt` via `-w` — the form to use when automating CeWL in scripts.

### Harvest email addresses only

Collect contact addresses for username lists and phishing pretexts.

```
cewl -e -n --email_file emails.txt http://target.example.com
```

Outcome: suppresses the wordlist entirely (`-n`) and writes every email address found while spidering to `emails.txt`; without `--email_file` the addresses print to stdout alongside the words.

### Add document metadata and digit-containing words

Passwords often mirror PDF/Office author fields or append years.

```
cewl --with-numbers -a -w cewl-meta.txt http://target.example.com
```

Outcome: keeps words containing digits in addition to pure-letter words and merges metadata extracted from documents discovered on the site (requires exiftool installed) into the output file.

### Spider a site behind HTTP authentication

Harvest an internal or protected application.

```
cewl --auth_type basic --auth_user user --auth_pass password http://target.example.com
```

Outcome: authenticates every spider request with the supplied credentials (Digest is also supported via `--auth_type digest`) and builds the wordlist from the authenticated pages.
