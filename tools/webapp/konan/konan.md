---
name: "konan"
categories:
  - blackarch-webapp
version: "23.7b5ac80"
homepage: "https://github.com/m4ll0k/Konan"
packaged-by: [blackarch]
blackarch-builddate: "2021-01-16"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "m4ll0k/Konan"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# konan

> Python 2 directory and file brute-forcer with a distinctive URL injection point — brute words into any position of a path, not just appended to it.

## What it's for

Konan (by m4ll0k) brute-forces directory and file names on web/application servers: multithreaded, multiple extensions, HTTP proxy support, text and JSON reports, user-agent randomization, response-size processing, recursive and sub-directory brute forcing, multiple HTTP methods. Its differentiator is the URL injection point: place `%%` anywhere in the target URL and the wordlist is substituted there, so you can brute force a path segment in the middle of a URL (e.g. `/api/%%/v1/users`) rather than only prefixes. The upstream GitHub repository is gone; the packaged Python 2 script still runs, but the tool is frozen in its 2018-era feature set — for maintained alternatives see gobuster/dirsearch-style notes in this vault.

## Typical use cases

### Brute force directories and files

The basic scan of a target.

```
konan -u http://target.example.com/
```

Outcome: streams a live table (`PERCENT - TIME - CODE - METHOD - LENGTH - URL`) of discovered paths, e.g. `200 - GET - 4958 - http://target.example.com/index.php`; the default wordlist and settings sweep the common paths.

### Brute words into a URL injection point

The feature that sets it apart.

```
konan -u "http://target.example.com/%%/index.php" -w wordlist.txt
```

Outcome: every wordlist entry is substituted for `%%` in the middle of the URL — finding the valid segment of a structured path (version dirs, tenant names, API namespaces) that appending words to the root cannot reach.

### Tune extensions, wordlist and case

Shape the candidate set.

```
konan -u http://target.example.com/ -w wordlist.txt -e php,html -f -p
```

Outcome: `-e` appends each listed extension to every word, `-f` forces requests on regardless of earlier results, and `-p`/`-l` transform the wordlist to upper/lower case — the standard knobs for hitting both `Admin` and `admin` style layouts.

### Filter by response code

Show only what matters.

```
konan -u http://target.example.com/ -o 200,301,302 -x 400,403,401
```

Outcome: `-o`/`--only` restricts output to the listed status codes and `-x`/`--exclude` drops the listed ones, so reports contain hits instead of noise; results are also written to the tool's text/JSON report files.

No corpus evidence — derived from archived upstream documentation (m4ll0k/Konan README via a 2020 snapshot; repo since deleted, packaged script verified as `konan.py` under Python 2).
