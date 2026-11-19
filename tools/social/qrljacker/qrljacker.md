---
name: "qrljacker"
categories:
  - blackarch-social
version: "222.defd5e3"
homepage: "https://github.com/OWASP/QRLJacking/tree/master/QRLJacker"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "OWASP/QRLJacking"
github-stars: 1582
github-pushed: "2025-08-07"
github-archived: false
github-issues: 70
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# qrljacker

> QRLJacker is a highly customizable exploitation framework to demonstrate "QRLJacking Attack Vector".

## What it's for

QRLJacker is the OWASP exploitation framework that demonstrates
QRLJacking: hijacking sessions of services that let users log in by
scanning a QR code (chat apps, mail, e-commerce). A grabber module opens
the target service's QR login page headlessly, relays the QR image to a
page you control, and when the victim scans your QR with their phone app,
the captured session appears in the framework as an interactive session
you can drive from your own browser. It is a Metasploit-style console
(modules, sessions, jobs, resource files) aimed at security awareness and
authorized testing. It requires Firefox and geckodriver; it does not run
on Windows, and every use case is against a service with the tester's
authorization.

## Typical use cases

### Run a QR grabber against a service

Capture a QR login session from a target service.

```
python3 QrlJacker.py
use grabber/whatsapp
run
```

Outcome: The module launches a headless Firefox instance, loads the
service's QR login page, and serves the live QR image on a local URL you
can forward to the victim; once they scan it, a hijacked session appears
in `sessions`.

### Interact with a captured session

Use the hijacked login from your own browser.

```
sessions -l
sessions -i 1
```

Outcome: `-l` lists captured sessions with type and creation time; `-i 1`
opens session 1, letting you operate the victim's logged-in service
session.

### Automate a run without the console

Script the framework for repeatable demonstrations.

```
python3 QrlJacker.py -x "use grabber/whatsapp;run"
```

Outcome: Executes the semicolon-separated console commands directly at
startup and continues into the interactive console with the module
running.

### Replay a saved command script

Reproduce a previous engagement's exact steps.

```
python3 QrlJacker.py -r resource.rc
```

Outcome: Runs every command stored in the resource file (e.g. saved via
the console's `makerc`) in order at startup.

*No corpus evidence — derived from upstream documentation.*
