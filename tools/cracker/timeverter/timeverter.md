---
name: "timeverter"
categories:
  - blackarch-cracker
version: "83.24b715e"
homepage: "https://github.com/D3vil0p3r/timeverter"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "D3vil0p3r/timeverter"
github-stars: 8
github-pushed: "2022-10-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# timeverter

> Brute-forces time-based tokens and converts between time domains.

## What it's for

Many web apps generate password-reset or session tokens straight from server time (e.g. `md5(time())`), so knowing roughly when a token was issued collapses the keyspace to a few thousand values. TimeVerter sweeps a ± range around a chosen time base (now, a UTC offset, or a known epoch), applies optional prefix/suffix, a hash algorithm and encodings, and either compares candidates against a known token offline or fires HTTP requests at a URL with each guess substituted for a `VERTER` placeholder. It doubles as a general date/epoch converter. It cannot attack HMAC-style TOTP tokens that mix in a secret — only tokens computable from the timestamp (plus attacker-known strings) alone.

## Typical use cases

### Convert dates, epochs and check the clock

Pick and sanity-check the time base for an attack.

```
timeverter -D 2026-11-08T01:13:37
timeverter -E 1794100417
timeverter -n
```

Outcome: prints the epoch equivalent of the date, the date equivalent of the epoch, and the current local time in both formats — the inputs every other mode is built on.

### Recover a token offline from its hash

Sweep ±3000 seconds around now, hashing each candidate timestamp and comparing to a captured token.

```
timeverter -n -g md5 -r 3000 -z a4e11f213f0bc314a043207dba6ca8ca
```

Outcome: iterates timestamps as md5 digests and compares each to the value given with `-z`; on a match reports the timestamp that generated the captured token — no network traffic is sent.

### Bruteforce a token in an HTTP parameter

Guess tokens against a live endpoint, flagging failures by response text.

```
timeverter -d 'submit=check&token=VERTER' -u http://198.51.100.10:8000/token.php -X POST -g md5 -n -fr 'Wrong token' -r 3000
```

Outcome: submits each candidate token in place of `VERTER`; responses matching the failure regex are skipped and non-matching (or `--matchregex`-matching) responses are reported as hits, along with the timestamp that produced them.

### Match millisecond tokens with a prefix

Adapt magnitude and format: milliseconds (×1000) and a fixed string before the timestamp.

```
timeverter -U +0:00 -r 3000 -g md5 -X POST -u http://198.51.100.10:8000/check -d 'userid=admin&token=VERTER' -fr 'Wrong token' -mul 1000 -p admin
```

Outcome: sweeps UTC-based timestamps multiplied by 1000 (millisecond domain), prefixes each with `admin`, submits them as the token value and reports non-failing responses.

*No corpus evidence — derived from upstream documentation.*
