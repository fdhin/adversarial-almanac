---
name: "dvwa"
categories:
  - webapp
version: "2.2.2-0parrot1"
homepage: "https://github.com/digininja/DVWA"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "digininja/DVWA"
github-stars: 13988
github-pushed: "2026-10-18"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 54
gh-activity-checked: "2026-11-09"
---

# dvwa

> Damn Vulnerable Web Application — a PHP/MySQL web application that is deliberately vulnerable, for practicing web security legally.

## What it's for

DVWA is a training application, not an attack tool: a PHP/MariaDB web app shipped with intentionally vulnerable modules — brute force, command injection, CSRF, file inclusion, file upload, SQL and blind SQL injection, reflected/stored/DOM XSS and more — so that security professionals can test their skills and their tools against a legal target, developers can see how the bugs arise, and instructors can teach in a controlled environment. Every module ships at four security levels (low, medium, high, impossible), where "impossible" is the reference secure implementation to compare against. Deploy it only on isolated lab systems you control — it is vulnerable by design and must never be internet-facing.

## Typical use cases

### Initialize and log in

First run of a fresh deployment.

```
cp config/config.inc.php.dist config/config.inc.php
```

then browse to `http://127.0.0.1/setup.php`, press `Create / Reset Database`, and log in at `http://127.0.0.1/login.php` with the documented defaults `admin` / `password`.

Outcome: the database is created and seeded, and the main menu lists every vulnerable module ready to practice on; the login page notes the defaults are trivially brute-forceable — itself part of the exercise.

### Point an automated scanner at it

Make the target tool-friendly by disabling authentication.

```
$_DVWA[ 'disable_authentication' ] = true;
$_DVWA[ 'default_security_level' ] = 'low';
```

(set in `config/config.inc.php`).

Outcome: with authentication disabled and the default security level set, every module is reachable without a login session or cookies — the upstream-documented workaround for scanners and fuzzers that cannot handle the login flow, letting a tool exercise the vulnerable endpoints directly.

### Work one module across all four security levels

Practice exploit-then-fix on a single bug class.

```
$_DVWA[ 'default_security_level' ] = 'impossible';
```

(or pick the level on the `DVWA Security` page).

Outcome: the same module now applies input validation, prepared statements, tokens or output encoding as appropriate — the built-in before/after pair: exploit the low level, then read the impossible level's source (viewable in-app per module) to see the exact mitigation.

No corpus evidence — derived from upstream documentation (official DVWA README configuration sections).
