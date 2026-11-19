---
name: "webspa"
categories:
  - blackarch-backdoor
version: "0.8"
homepage: "http://sourceforge.net/projects/webspa/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "OWASP/WebSpa"
github-stars: 15
github-pushed: "2015-05-19"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webspa

> A web knocking tool, sending a single HTTP/S to run O/S commands.

*No corpus evidence — derived from upstream documentation (the archived OWASP mirror of the SourceForge release; the release ships a README and INSTALL).*

## What it's for

WebSpa (by Yann Cam, package `net.seleucus.wsp`) is "web knocking": the port-knocking idea moved to HTTP. Instead of opening a firewall port, a single, ordinary-looking HTTP or HTTPS request — carrying an action number and a passphrase-derived value, with scrypt-based key derivation in the crypto layer — authorizes the server to execute one predefined operating-system command. The Java server maps each numbered action to a command; the client interactively builds and sends the knock request. The point is that nothing is listening in a way an attacker can use without the passphrase: no shell is exposed, only whitelisted one-shot commands fire. It is a Java 1.6+ standalone jar (version 0.8 ships `webspa-08.jar`, wrapped by `webspa.sh`), dormant since 2015 — the GitHub mirror is archived and SourceForge is its historic home. Treat it as a small, stable, finished tool rather than an actively developed one.

## Typical use cases

### Sanity-check the jar

```
java -jar webspa-08.jar -version
```

Outcome: prints the version and exits — the quickest confirmation that the JRE is working. The bundled `webspa.sh` wrapper does the same via `./webspa.sh -version` once made executable; it only sets heap sizes around the same jar. Running the jar with no option at all prints the help.

### Run the knocking server

```
java -jar webspa-08.jar -server
```

Outcome: starts the WebSpa server side, which waits for authorized single HTTP/S requests and executes the operating-system command mapped to the action number in each valid knock. This is the component that runs persistently on the host you want to command remotely.

### Generate a knock from the client

```
java -jar webspa-08.jar -client
```

Outcome: starts the interactive client used to compose and send the knock — the single HTTP/S request that authorizes a chosen numbered command on the server. Run it from any machine that can reach the server's URL.

### Print the option summary

```
java -jar webspa-08.jar -help
```

Outcome: prints the usage message listing the four switches (`-client`, `-server`, `-version`, `-help`).
