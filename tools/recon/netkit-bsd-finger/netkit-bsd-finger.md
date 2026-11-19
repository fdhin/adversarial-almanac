---
name: "netkit-bsd-finger"
categories:
  - blackarch-recon
version: "0.17"
homepage: "http://ftp.linux.org.uk/pub/linux/Networking/netkit"
packaged-by: [blackarch, aur]
blackarch-builddate: "2017-04-08"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# netkit-bsd-finger

> The classic BSD finger client ported to Linux: queries local account data or a remote fingerd for user information.

## What it's for

This is netkit's port of BSD finger (0.17), the user-information lookup client for the finger protocol. Locally it reads the password file and the user's home directory (`.plan`, `.project`, `.pgpkey`, `.forward`); remotely, `finger user@host` opens the finger service on TCP/79 of a host running fingerd and returns whatever that server discloses about the user — login, real name, office, phone, login and idle times, mail status, and plan files. On networks where fingerd is still exposed it is one of the simplest username-enumeration and user-activity probes available; most modern hosts disable fingerd, so the remote form mostly matters against legacy or embedded targets. It only queries — it cannot change anything, and it does no scanning itself. Sibling netkit notes: [[netkit-rusers]] and [[netkit-rwho]] cover the same era of RFC-style host status protocols.

## Typical use cases

### Query a user on a remote finger service

Enumerate account details across the network boundary.

```
finger user@target.example.com
```

Outcome: prints the remote account's login, real name, home directory, shell, login/idle times, mail status, and `.plan`/`.project` contents (long format is the default for a single remote user); an empty or refused reply tells you fingerd is filtered or the user does not exist.

### Short-format multi-user listing

Compare several accounts at a glance.

```
finger -s user root admin@target.example.com
```

Outcome: one tabular line per user (login, name, tty, idle, login time, office, phone) instead of the multi-line long format.

### Match against real names

Find logins when you only know the person's name.

```
finger -l john
```

Outcome: by default finger also matches its argument against users' real-name (GECOS) fields, so this returns full records for every account whose login or real name matches "john"; use `-m` to restrict matching to login names only.

### Long format without plan files

Get the account record without dumping home-directory files.

```
finger -lp user@target.example.com
```

Outcome: same long-format record as `-l`, but `-p` suppresses the `.plan`, `.project`, and `.pgpkey` file contents.

*No corpus evidence — derived from upstream documentation (finger(1) manpage from the packaged bsd-finger 0.17 source).*
