---
name: "sshpass"
categories:
  - corpus-automation
homepage: "https://sourceforge.net/projects/sshpass/"
origin: "corpus"
packaged-by: [arch, nixpkgs]
arch-builddate: "2024-07-13"
meta-checked: "2026-11-09"
github-repo: "kevinburke/sshpass"
github-stars: 271
github-pushed: "2017-07-31"
github-archived: false
github-issues: 11
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sshpass

> Feed a password to ssh, scp, or rsync non-interactively so scripted tasks run unattended.

## What it's for

sshpass runs another command (almost always `ssh`, `scp`, or `rsync` over ssh) inside a dedicated pseudo-terminal and answers the `Password:` prompt for you, which lets loops, cron jobs, and one-liners authenticate without a human typing. Reach for it when key-based authentication is not available on the host you are connecting to; if you can install a key, prefer that — sshpass stores a plaintext password and `-p` exposes it in the process list to every local user. It answers exactly one password prompt: it does not handle multi-step keyboard-interactive challenges or one-time-password schemes, and it never confirms unknown host keys (it exits with code 6 instead).

## Typical use cases

### Log in to a remote host without a password prompt

Open an interactive shell on a host where only password authentication is offered.

```
sshpass -p 'S3cret!' ssh user@target.example.com
```

Outcome: ssh's `Password:` prompt is answered automatically and you land in a shell on the remote host; exit code 5 means the password was wrong.

### Automate connections to a host whose key is not yet known

Unattended runs die on the yes/no new-host-key prompt, so combine sshpass with OpenSSH's strict-host-key bypass.

```
sshpass -p 'S3cret!' ssh -o stricthostkeychecking=no user@198.51.100.10 'hostname -I'
```

Outcome: the host key is accepted automatically and the remote command's output is printed; without the `-o` option sshpass exits with code 6 on first contact.

### Copy files to and from a password-only host with scp

Push a file to the remote host (add `-r` before the source for a whole directory); swap the arguments to pull a file back.

```
sshpass -p 'S3cret!' scp psk.conf user@target.example.com:/tmp/
```

Outcome: the file is transferred and scp prints its progress lines; a second run overwrites the remote copy.

### Sync a directory over SSH with rsync

rsync uses ssh as its transport, so sshpass covers its password prompt the same way.

```
sshpass -p 'S3cret!' rsync -avzp --whole-file image.bin user@target.example.com:/backup/
```

Outcome: rsync connects without prompting, transfers only missing/changed blocks, and lists each file it copies.

### Open a local port forward without typing the password

Tunnel a remote internal service to a local port in one scripted command.

```
sshpass -p 'S3cret!' ssh -N -L 8080:192.168.3.1:80 user@target.example.com
```

Outcome: the SSH tunnel is established without any prompt and stays up until killed; browsing to `127.0.0.1:8080` now reaches the remote service.

### Keep the password off the command line

The manpage calls `-p` the least secure source because any local user can read it via `ps`; the environment variable avoids that.

```
SSHPASS='S3cret!' sshpass -e ssh user@target.example.com
```

Outcome: sshpass reads the password from the `SSHPASS` variable and the login proceeds exactly as with `-p`, but the secret is not visible in the process list (`-f filename`, first line of a file, is the file-based equivalent).
