---
name: "azazel"
categories:
  - blackarch-backdoor
version: "15.a41fbb5"
homepage: "https://github.com/chokepoint/azazel"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "chokepoint/azazel"
github-stars: 810
github-pushed: "2024-03-07"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# azazel

> Userland LD_PRELOAD rootkit for Linux (Jynx lineage) — hides files, processes, logins and remote connections from local inspection while offering hidden accept()-based PTY backdoors and a PAM bypass, all configured through generated compile-time constants.

## What it's for

Post-exploitation concealment on a compromised Linux host. The payload is a shared library (built as `libselinux.so`) that interposes security-relevant libc calls — `accept`, `open`, `readdir`, `execve`, the `pam_*` family, `pcap_loop` and more — so that once it is preloaded on a host, the host's own tooling lies to its operator: `ps`, `lsof`, `ldd`, `unhide` and local sniffers see a sanitized world. Remote access stays available through two hidden `accept()` backdoors that trigger only for connections whose *source port* falls in a configured range: a plaintext full-PTY backdoor (default source ports 61040–61050) and a CryptHook-encrypted variant (61051–61060); a PAM backdoor (port 61061) additionally lets a configured blind-login user (default `rootme`) into `ssh`/`su` with any password, and utmp/wtmp entries are cleaned per-PTY. All secrets and ranges come from `config.py`, which generates the `const.h` compile-time constants (default password `changeme`, key material "Hello NSA"/`changeme`) — change them before deploying. It is 32/64-bit userland only: no kernel component, so root privileges and preloading (via `/etc/ld.so.preload`) are prerequisites, and it does not survive from a host you cannot already control.

## Typical use cases

### Connect to the hidden plaintext backdoor

Open a concealed root PTY on a host where the library is preloaded.

```
socat -,raw,echo=0 TCP:target:22,bind=:61040
```

Outcome: the connection to any listening service (here sshd) is made *from* local source port 61040, which the hooked `accept()` recognizes as the backdoor magic; after pasting the configured password you get a full root PTY. The connection does not show up in the target's `netstat`/`lsof` output, and paste-then-send matters — socat would otherwise fire the first keystroke before the password prompt.

### Log in through the PAM backdoor

Use the blind-login account bypassed by the hooked PAM stack.

```
ssh rootme@target
```

Outcome: the hooked `pam_authenticate` accepts the configured blind user (default `rootme`) with any password and the session lands as root; the same bypass applies to `su rootme`. The account exists only to the rootkit — it is not in the real user database.

### Hide an artifact with the magic string

Make a payload invisible to the host's own enumeration.

```
mv stage2.bin __stage2.bin
```

Outcome: the hooked `readdir`/stat paths filter the configured magic substring (default `__`), so the renamed file disappears from `ls`, file dialogs and the like for every dynamically linked process on the host while remaining fully usable by path.

*No corpus evidence — derived from upstream documentation.*
