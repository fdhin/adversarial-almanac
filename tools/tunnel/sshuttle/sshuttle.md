---
name: "sshuttle"
categories:
  - tunnel
homepage: "https://github.com/sshuttle/sshuttle"
origin: "pentest-book"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: python
license: LGPL-2.1
github-repo: "sshuttle/sshuttle"
github-stars: 13662
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
---

# sshuttle

> "Poor man's VPN": route any subnet (or all traffic) through an SSH server you can reach — no VPN protocol, no root, and nothing to install on the remote side.

## What it's for

sshuttle is a transparent proxy that behaves like a VPN but is built entirely on SSH: it intercepts outgoing TCP (and optionally DNS) on your local machine and forwards matching traffic through an SSH session to a server that regenerates the connections on its end. The classic case it solves: you have SSH access to a box inside a remote network and need to reach that network's hosts and ports — without admin access on the remote side, without a VPN server, and without one port-forward per host. It needs root (sudo) on the local client only; the remote server just needs a sufficiently new Python, because sshuttle uploads and runs its own source code there. It runs on Linux, FreeBSD and macOS (Windows experimental), can run several tunnels at once, and — started on a router — can forward traffic for a whole local subnet. For pentest work it is the standard quick pivot: one command turns a set of SSH credentials into routed access to internal address space. It avoids OpenSSH's TCP-over-TCP forwarding penalty by acting as a transparent proxy instead of a SOCKS tunnel.

## Typical use cases

### Route all traffic through the SSH server

The one-flag VPN: everything you connect to goes via the remote host.

```
sudo sshuttle -r user@target.example.com 0/0
```

Outcome: after the local sudo password and the SSH password (or keys), a transparent proxy is installed locally for all IPv4 addresses — `0/0` is shorthand for 0.0.0.0/0; the SSH connection endpoint itself is automatically excluded from forwarding. Any TCP session you now start to any address is tunneled and exits from the remote server; Ctrl-C tears the VPN down.

### Pivot into specific internal ranges

Only route the ranges behind the SSH box and keep the rest of your traffic local.

```
sshuttle --dns -NHr user@target.example.com 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16
```

Outcome: the three RFC1918 ranges are routed through the tunnel, `--dns` also resolves via the remote side's DNS, `-N` (auto-nets) adds subnets the server itself suggests from its routing table, and `-H` (auto-hosts) merges remote hostnames into your `/etc/hosts` for the session — the "my VPN broke, reach internal hosts fast" form.

### Tunnel everything except your local network

Keep local subnet traffic out of the tunnel while routing the rest.

```
sudo sshuttle -r user@target.example.com -x 192.168.0.0/16 0/0
```

Outcome: all traffic is tunneled except destinations in 192.168.0.0/16 (`-x/--exclude` takes a subnet, repeatable), so LAN devices and the SSH endpoint keep direct paths while everything else egresses from the remote server.

### Run the tunnel as a background daemon

Keep the pivot alive without a terminal.

```
sudo sshuttle -D --pidfile=/run/sshuttle.pid -r user@target.example.com 0/0
```

Outcome: sshuttle daemonizes (`-D`), writes its PID to the given file, and keeps the routes up in the background; `sudo kill $(cat /run/sshuttle.pid)` or `--syslog`-logged errors let you manage it headless.

### Let a user start tunnels without the local sudo password

Generate a sudoers snippet so sshuttle can elevate without a password prompt.

```
sshuttle --sudoers-no-modify --sudoers-user %sudo
```

Outcome: prints a ready-to-install sudoers.d configuration for the given user or group together with the suggested install path; upstream warns this is insecure — with some cleverness it lets those users run arbitrary commands as root via `--ssh-cmd`, so treat it as a convenience on throwaway pentest boxes only.

No corpus evidence — derived from upstream documentation.
