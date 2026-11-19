---
name: "loic"
categories:
  - blackarch-networking
version: "2.9.9.99"
homepage: "https://github.com/NewEraCracker/LOIC"
packaged-by: [blackarch, aur]
blackarch-builddate: "2017-10-20"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "NewEraCracker/LOIC"
github-stars: 2965
github-pushed: "2024-01-09"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# loic

> Low Orbit Ion Cannon — a Windows network stress tool (TCP/UDP/HTTP) operable standalone, from its GUI, or as an IRC-driven "hivemind".

## What it's for

LOIC is the well-known open source network stress testing tool written in C#, maintained on GitHub by NewEraCracker and based on Praetox's original SourceForge project. Each instance floods a chosen target with TCP, UDP or HTTP traffic from a simple GUI (target address, port, method, speed slider, message). Its distinctive feature is hivemind mode: instances connect to an IRC channel and take target settings and start/stop commands from messages or the channel topic set by an operator, turning many voluntary clients into one coordinated load generator. It is for stress-testing systems you own or are contracted to test — the upstream disclaimer forbids illegal use — and it performs no exploitation; it is purely volume. The BlackArch package ships the Windows binaries under `/usr/share/windows/loic` (runnable under Mono) since the .NET GUI is the tool's whole interface; the repo is archived and development has stopped.

## Typical use cases

### Launch the GUI on Linux via Mono

```
mono /usr/share/windows/loic/LOIC.exe
```

Outcome: opens the LOIC window where you set a target URL/IP, port, attack method (TCP/UDP/HTTP) and thread speed, then start and stop the flood with the GUI buttons — the standalone, single-client mode.

### Join an IRC hivemind

```
LOIC.exe /hivemind irc.example.com
```

Outcome: the instance connects to `irc.example.com:6667` and joins channel `#loic`, ignoring its local target settings and waiting for `!lazor` commands. Add a port and channel to override: `LOIC.exe /hivemind irc.example.com 6697 #loic-ops` connects to `irc.example.com:6697/#loic-ops`.

### Run hidden, driven only by IRC

```
LOIC.exe /hidden /hivemind irc.example.com
```

Outcome: same hivemind connection but with no visible GUI — the client keeps running in the background and is controlled exclusively through IRC commands.

### Control hivemind clients from IRC

```
!lazor targetip=198.51.100.10 port=80 method=tcp wait=false random=true start
```

Outcome: every hivemind client on the channel adopts the embedded settings and begins flooding 198.51.100.10:80 over TCP; `!lazor stop` halts them, and `!lazor default` resets options. As channel operator the same directives can be baked into the topic — remember to strip `start` from the topic to end a run.

*No corpus evidence — derived from upstream documentation.*
