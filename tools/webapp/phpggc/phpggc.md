---
name: "phpggc"
categories:
  - blackarch-webapp
version: "725.f8aebde"
homepage: "https://github.com/ambionics/phpggc"
packaged-by: [blackarch]
blackarch-builddate: "2025-10-05"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "ambionics/phpggc"
github-stars: 3916
github-pushed: "2025-09-29"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# phpggc

> Library and command-line generator of PHP `unserialize()` gadget chains — the ysoserial equivalent for PHP, covering Laravel, Symfony, Guzzle, Monolog, Drupal, WordPress and more.

*Corpus-grounded (Laravel/RCE10 and Laravel/RCE7 generation, `-a` ASCII-strings, `--fast-destruct -u` Guzzle/FW1 file-write patterns); every flag verified against the ambionics/phpggc README and the packaged option parser in `lib/PHPGGC.php`.*

## What it's for

When a PHP application passes attacker-controlled data to `unserialize()` (or exposes a `phar://` stream surface) and you do not have its source, exploiting the bug means hand-crafting an object graph of the framework's own classes — tedious and version-sensitive. PHPGGC ships a library of pre-built gadget chains across dozens of frameworks (RCE, file write, file read, include), and generates the serialized payload for a chain with one command. It only produces the payload: you still need an injection point that reaches `unserialize()`, and the chain must match the framework (and roughly the version) the target runs — `-l` shows which version range each chain targets. Chain names are case-insensitive (`laravel/rce10` and `Laravel/RCE10` are the same chain).

## Typical use cases

### List chains for the target framework

```
phpggc -l laravel
```

Outcome: prints a table of every Laravel chain — name, affected framework version range, exploit type (RCE, File Write, File Read…) and trigger vector (`__destruct`, `__toString`, …). Run `phpggc -l` bare for all frameworks; `phpggc -i laravel/rce10` then prints one chain's details plus its exact invocation form.

### Generate a serialized RCE payload

```
phpggc Laravel/RCE10 system id
```

Outcome: prints the serialized object that, when unserialized by the target, calls `system("id")`. Argument shape follows the chain type: "RCE (Function call)" chains take a function and its argument (`system id`), "RCE (PHP code)" chains take a code string (`'phpinfo();'`), and "RCE (Command)" chains take the bare command. Pipe the output into a file or the delivery request.

### Emit a payload that survives NULL-byte mangling

```
phpggc -a Laravel/RCE7 system id
```

Outcome: generates the payload with the `S` serialization format, writing non-ASCII bytes (including the NUL bytes protected/private properties carry) as `\00`-style hex escapes, so the string survives transports that strip or choke on binary. The same trick defeats contexts that reject raw NULL bytes.

### Write a file with an encoded, fast-destructing chain

```
phpggc --fast-destruct -u Guzzle/FW1 /var/www/html/shell.php /tmp/shell.php
```

Outcome: builds the Guzzle file-write chain that copies local `/tmp/shell.php` to the remote path on unserialize, URL-encodes it for GET/cookie transport (`-u`), and `--fast-destruct` triggers the destructor immediately at `unserialize()` rather than script end — more reliable when the target code throws after the call. Encoders chain in order (`-b` base64, `-u` URL, `-s` soft/readable URL); `--fast-destruct` is itself order-sensitive and belongs first.

### Package the chain as a PHAR file

```
phpggc -p phar -o /tmp/payload.phar Monolog/RCE1 system id
```

Outcome: instead of printing a serialized string, writes `/tmp/payload.phar` containing the payload, for attacks that reach `phar://` stream handlers (file_exists, fopen on a phar URL) rather than a direct `unserialize()`. `-p zip`/`-p tar` select other archive formats and `-pj image.jpg` builds a JPEG/PHAR polyglot.

### Test a chain against a local install of the framework

```
phpggc Monolog/RCE2 --test-payload
```

Outcome: run from inside a composer project that actually contains the target framework, this generates the chain and immediately unserializes it locally, printing `SUCCESS: Payload triggered !` (exit code 0) or failing with exit code 1 — a fast way to confirm the target's dependency versions are in range before spending the shot.
