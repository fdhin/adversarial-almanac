---
name: "php-findsock-shell"
categories:
  - blackarch-webapp
version: "2.b8a984f"
homepage: "https://github.com/pentestmonkey/php-findsock-shell"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-04"
build-system: other
license: GPL2
meta-checked: "2026-11-09"
github-repo: "pentestmonkey/php-findsock-shell"
github-stars: 17
github-pushed: "2015-05-29"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# php-findsock-shell

> PHP + C webshell pair from pentestmonkey that attaches an interactive shell to the client's own TCP connection.

## What it's for

For situations where you can upload files to a web server but firewalling blocks both bind and reverse shells. The PHP script takes the client's `REMOTE_ADDR`/`REMOTE_PORT`, and a small compiled C helper walks the server's file descriptors to find the very socket your request arrived on, then spawns a shell on it — so the shell traffic rides the port that is already permitted (typically 80/443). It is not a command-over-HTTP webshell: it needs two files (`php-findsock-shell.php` plus the compiled `findsock` binary) uploaded to the same directory, and the C binary must match the target's OS/architecture. Kali also ships both files under `/usr/share/webshells/php/`.

## Typical use cases

### Compile the C helper for the target

Build the helper that will be uploaded next to the PHP script.

```
gcc -o findsock findsock.c
```

Outcome: produces the `findsock` binary; cross-compile if the web server runs a different OS or architecture than your workstation. Both `findsock` and `php-findsock-shell.php` must end up in the same directory on the target.

### Get an interactive shell over the original connection

Trigger the shell from a raw TCP client (netcat or socat — not a browser, since this is a fully interactive shell).

```
nc -v target.example.com 80
GET /php-findsock-shell.php HTTP/1.0
```

Outcome: the PHP script runs `findsock` against your source address/port; the helper attaches `/bin/sh` to that socket and you get a shell prompt (verify with `id`) — a fully interactive session usable for `su`, logins or shell upgrades, without any new outbound connection.

*Provenance: no corpus evidence — derived from upstream documentation (repo sources `php-findsock-shell.php`/`findsock.c`, verified 2026-11).*
