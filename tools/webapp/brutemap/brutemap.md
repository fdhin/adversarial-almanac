---
name: "brutemap"
categories:
  - blackarch-webapp
version: "65.da4b303"
homepage: "https://github.com/brutemap-dev/brutemap"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "brutemap-dev/brutemap"
github-stars: 172
github-pushed: "2019-06-13"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# brutemap

> Browser-driven login bruteforcer that finds the login form for you.

## What it's for

Brutemap automates testing accounts against a site's login page using dictionary attacks. It drives a real browser through Selenium, locates the login form and its parameters automatically (no need to identify the CMS or write per-app configs), and can also run an authentication-bypass attack mode that fires SQL injection payloads at the login instead of wordlist passwords. It needs a Selenium WebDriver installed and working. Username, password and email-domain values may be given inline or as files, making it usable for both single-target checks and large credential lists.

## Typical use cases

### Test one account with a few passwords

Point brutemap at a login URL with an inline username and password candidates.

```
brutemap -t http://target.example.com/admin/login.php -u admin -p Password1 default changeme
```

Outcome: opens the target in the WebDriver browser, identifies the login form, tries each password for `admin`, and reports any working credential; use `--twin` to pair each username with the password at the same list position instead.

### Run a full credential-list attack

Feed usernames and passwords from files instead of the command line.

```
brutemap -t targets.txt -u /usr/share/wordlists/users.txt -p /usr/share/wordlists/passwords.txt -o result.html
```

Outcome: iterates every target × username × password combination through the browser and writes the found accounts to `result.html`; `-d gmail.com` would additionally enumerate email-domain-based usernames from a domains file.

### Try SQL injection login bypass instead of passwords

Skip the password list and attempt authentication bypass payloads.

```
brutemap -t http://target.example.com/login.php -u admin --sqli-bypass
```

Outcome: submits the bundled SQL injection payloads (e.g. classic `' OR 1=1--` forms) as the credential, flagging any payload that authenticates; `--sqli-payloads` instead pairs the username with SQLi payload suffixes.

No corpus evidence — derived from upstream documentation and source (argparse definitions).
