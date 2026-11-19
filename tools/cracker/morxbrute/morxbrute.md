---
name: "morxbrute"
categories:
  - blackarch-cracker
version: "1.01"
homepage: "http://www.morxploit.com/morxbrute/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morxbrute

> Dictionary brute forcer for HTTP login forms, driven by per-application payload files.

## What it's for

MorXBrute is a single-threaded Perl dictionary attacker for web login forms. For each candidate password it sends a GET or POST request and decides success from a response regex — normally a `Location:` redirect header that only appears on a valid login. Targets are described by one-line "payload" files (HTTP method, login script path, parameter string, cookie value, success regex), and the tool ships payloads for WordPress 3.7.1, cPanel (GET and POST variants), Horde 5.1.0, XOOPS 2.5.6 and everyone.net. It cracks one user at a time and has no SSL support, so it fits targeted testing of a known application rather than mass spraying. The author's site has expired; distro packages keep the tool available.

## Typical use cases

### Brute force a WordPress admin password

Run the tool against a WordPress target with the bundled payload.

```
morxbrute target.example.com:80 admin wordlist.txt payloads/wordpressv3.7.1
```

Outcome: POSTs to the WordPress login script with the admin username and each candidate password, printing `[-] word -> Failed` per miss; on success prints `CRACKED! Your password is <password>` with the matching wordlist line number and elapsed seconds. On BlackArch the bundled payloads live under `/usr/share/morxbrute/payloads/`.

### Attack a GET-based login

Use the GET-variant cPanel payload and the service port.

```
morxbrute target.example.com:2082 user wordlist.txt payloads/cpanelgetprov1.0
```

Outcome: same dictionary loop with credentials placed in the query string instead of the request body; success is detected by the payload file's response regex rather than any hardcoded string, so the output is identical to the POST case.

### Add a new application with a custom payload

Write the one-line payload, then run against your own target.

```
printf '%s\n' 'POST,login.php,login=$user&password=$pwd,MorXploit,Location:(.*)' > payloads/myapp
morxbrute target.example.com:80 user wordlist.txt payloads/myapp
```

Outcome: the five comma-separated fields — method, login script path, parameter string with the reserved `$user`/`$pwd` placeholders, cookie value, and success regex — teach MorXBrute a new application; the run then behaves exactly like a bundled payload. Derive the fields by capturing one failed and one successful login with a sniffer or the browser's developer tools.

*No corpus evidence — derived from upstream documentation.*
