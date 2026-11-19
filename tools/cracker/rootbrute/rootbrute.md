---
name: "rootbrute"
categories:
  - blackarch-cracker
version: "0.1"
homepage: "http://www.packetstormsecurity.org/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rootbrute

> Local root password bruteforcer that replays a wordlist through `su` using pexpect.

## What it's for

A small Python 2 script (by d3hydr8, darkc0de) for auditing the local root
password on a Unix host you control. It spawns `su`, feeds it each word from
a wordlist, and watches for a root shell prompt; on success it prints the
password and hands over an interactive root session. It performs no network
operations and takes no options. Failure detection is a hardcoded string
(`su: incorrect password`, GNU su), so on systems whose `su` prints a
different message the `LOGIN_ERROR` constant in the script must be adjusted
first.

## Typical use cases

### Test the local root password against a wordlist

Run the tool with a single argument: the path to a newline-separated
wordlist.

```
rootbrute passwords.txt
```

Outcome: prints the number of words loaded, then tries each one through
`su`; on a hit it prints `[!] Root Password: <word>`, runs `whoami` and
opens an interactive root shell.

*No corpus evidence — derived from upstream documentation.*
