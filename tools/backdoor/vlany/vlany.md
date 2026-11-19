---
name: "vlany"
categories:
  - blackarch-backdoor
version: "255.9ef014a"
homepage: "https://github.com/mempodippy/vlany"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mempodippy/vlany"
github-stars: 990
github-pushed: "2020-12-11"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vlany

> Linux LD_PRELOAD rootkit (x86 and x86_64 architectures) — hides files, processes, users and network activity, and ships PAM/accept() backdoors.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A userland Linux rootkit loaded through `/etc/ld.so.preload`, by mempodippy. It hides files via extended attributes, processes via a magic GID, backdoor users via hooked utmp/PAM calls, and the backdoors' network connections from netstat-style tools; it also hooks libpcap so sniffers miss the traffic. Anti-analysis is layered: `ptrace` broken to kill debugging, `strings` broken against the rootkit library, `/proc/self/maps`/`smaps`/`numa_maps` forged to hide the library path, and `ldd`, `chkproc`, `rkhunter` and `unhide` are detected and dodged by temporarily unlinking `ld.so.preload` while they run. Persistence is aggressive — the library's constructor and destructor rewrite `ld.so.preload` at the start and end of every process, so deleting the entry does not stick. Access comes through "owner shells" (root plus a magic environment variable or the magic GID) reached via two built-in backdoors: a PAM backdoor giving ssh/sftp with a hidden, hashed-password user, and a Jynx2-derived accept() backdoor that turns a magic port range on any restarted service into a netcat-triggered `/bin/sh`; a snodew reverse shell is also bundled. Know the state of the project: unmaintained since 2020 (its README points to `naworkcaj/bdvl` as the maintained successor), known to fail installing above CentOS 6.6, and experimental enough that some systems brick on reboot. Installation lives in `install.md`.

## Typical use cases

### Reach an owner shell through the PAM backdoor

```
./misc/ssh.sh
```

Outcome: the helper shipped in the source tree's `misc/` directory — run it bare to see its usage — connects to the hidden PAM port with your backdoor user and password (defined at install time) and drops you into an owner shell that can see the rootkit's hidden files and processes. Logging in by plain ssh instead leaves a visible connection in the logs.

### List the rootkit's exclusive commands

```
./help <execve_password>
```

Outcome: from inside an owner shell, prints every vlany-exclusive `./`-prefixed command available with your execve password (set during installation) — including utilities to hide newly created files or directories (anything you create stays visible until hidden) and `enter_lxc`, which spins up a throwaway hidden LXC container destroyed on exit.

### Get a shell through the accept() backdoor

```
ncat target.example.com 50001
```

Outcome: connects to a port inside the magic port range configured at install time, on a service that was restarted after vlany was installed (the installer restarts sshd itself); you are prompted for the runtime password and dropped into `/bin/sh`. Add `--ssl` when the target service listens with SSL — the hook supports both plaintext and encrypted triggers.
