---
name: "brutexss"
categories:
  - blackarch-webapp
version: "54.ba753df"
homepage: "https://github.com/shawarkhanethicalhacker/BruteXSS"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-11"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "shawarkhanethicalhacker/BruteXSS"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# brutexss

> Interactive XSS bruteforcer that fires a payload wordlist at GET or POST parameters.

## What it's for

BruteXSS (by Shawar Khan) bruteforces Cross-Site Scripting: you give it a URL (and POST data for forms), it injects every payload from a wordlist — a default `wordlist.txt` ships with it — into each parameter and reports which parameters are actually vulnerable. It handles both GET query strings and POST body parameters, which covers classic search boxes and form-driven applications. It is a Python 2.7 tool built on mechanize/colorama, and it works through an interactive wizard rather than command-line flags; the upstream repository is gone (verified via the Internet Archive), so the packaged copy is the reference.

## Typical use cases

### Bruteforce a URL's parameters with the payload wordlist

Start the wizard and walk it through a GET target.

```
brutexss
```

Outcome: the wizard asks for method (`g`/`p`), the URL such as `http://target.example.com/search/?q=value`, and the wordlist path (Enter accepts the default `wordlist.txt`); it then injects each payload into every parameter, prints `[!] XSS Vulnerability Found!` with the parameter and working payload, and finishes with a table summarising each parameter as Vulnerable or Not Vulnerable. For POST targets choose `p` and additionally supply the body string `param=value&param2=value2`.

No corpus evidence — derived from the archived upstream README (repo no longer exists).
