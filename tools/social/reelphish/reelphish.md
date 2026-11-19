---
name: "reelphish"
categories:
  - blackarch-social
version: "5.dc1be33"
homepage: "https://github.com/fireeye/ReelPhish"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "fireeye/ReelPhish"
github-stars: 518
github-pushed: "2023-08-11"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# reelphish

> A Real-Time Two-Factor Phishing Tool.

## What it's for

ReelPhish (FireEye, 2018) defeats the "phish, then replay" latency
problem in two-factor phishing: instead of collecting credentials and
using them later, it relays them to the real site in real time. The tool
listens locally for credentials POSTed by your phishing page (sample PHP
site code ships in the repo), then immediately drives a Selenium browser
to the real login URL, submits the credentials, and — when the site
presents a second factor page — captures the OTP from the victim and
feeds it through too, keeping the session alive while the tester works.
It is Python 2.7 legacy code with an archived upstream, intended for
authorized red-team exercises; browser drivers (renamed to
ChromeDriver.exe/FFDriver.exe/IEDriver.exe) must be provided alongside.

## Typical use cases

### Relay credentials to a login page in real time

Core man-in-the-loop phishing relay.

```
python ReelPhish.py --browser Chrome --url https://example.com/login
```

Outcome: Starts the listener (credentials are expected on localhost:2135,
typically reverse-SSH-tunneled from the phishing host); each received
credential set is immediately submitted to the real login URL in a
Selenium-driven Chrome window.

### Handle multi-page and two-factor logins

Phish sites that split password and OTP across pages.

```
python ReelPhish.py --browser Chrome --url https://example.com/login --numpages 2
```

Outcome: Tracks the session across two authentication pages, so the
second factor the victim enters on the phishing page is relayed to the
real second page while the first submission is still valid.

### Debug the relay and customize submission

Diagnose failed logins or unusual form element names.

```
python ReelPhish.py --browser Chrome --url https://example.com/login --submit "loginButton" --logging debug
```

Outcome: Clicks the named element instead of the default submit control
and prints verbose event logging for every step of the Selenium session;
`--override` additionally ignores missing form elements.

*No corpus evidence — derived from upstream documentation.*
