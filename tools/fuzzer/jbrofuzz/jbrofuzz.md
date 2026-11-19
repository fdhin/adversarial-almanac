---
name: "jbrofuzz"
categories:
  - blackarch-fuzzer
version: "2.5"
homepage: "http://sourceforge.net/projects/jbrofuzz/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "twilsonb/jbrofuzz"
github-stars: 8
github-pushed: "2014-08-16"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jbrofuzz

> OWASP web application fuzzer that replays an HTTP(S) request with payloads from a built-in generator database substituted at every position you mark.

*No corpus evidence — derived from upstream documentation.*

## What it's for

JBroFuzz 2.5 (codename "Oro", 2010) is a stateless network protocol fuzzer for web applications. You paste a request, mark fuzz points, choose payload generators from its local database (XSS, SQL injection, buffer overflow and similar categories), and run; every request/response pair is recorded and can be graphed. The `jbrofuzz` wrapper launches the Java Swing GUI, and a small set of command-line arguments can pre-configure a session or auto-start it. It does not discover or spider URLs — you supply the exact request to fuzz. The package also ships example `.jbrofuzz` session files under `/usr/share/jbrofuzz/examples`.

## Typical use cases

### Launch the GUI

Open the fuzzing workbench to define a request interactively.

```
jbrofuzz
```

Outcome: starts the JBroFuzz window (Payloads, Fuzzing, Graphing tabs); opening one of the `.jbrofuzz` example sessions from `/usr/share/jbrofuzz/examples` shows a fully configured request with fuzz points.

### Show the command-line options

List what can be scripted from the launcher.

```
jbrofuzz -h
```

Outcome: prints the command-line help (`-u`, `-r`, `-f`, `-fs`, `-fe`, `-R`, ...) and exits without opening the GUI.

### Pre-fill a request and auto-start the run

Skip the manual GUI setup for a repeatable fuzzing configuration.

```
jbrofuzz -u http://target.example.com/ -r "GET / HTTP/1.0" -R
```

Outcome: opens the GUI with the URL and request fields pre-filled from the arguments and immediately starts fuzzing (`-R`), so the session begins without clicking through the panels.
