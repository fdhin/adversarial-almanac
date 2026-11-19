---
name: "voiper"
categories:
  - blackarch-voip
version: "0.07"
homepage: "http://voiper.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "gremwell/voiper"
github-stars: 19
github-pushed: "2015-09-29"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# voiper

> VoIPER — "nnp"'s Sulley-based SIP fuzzing and research toolkit, with RFC 4475 torture tests, crash detection and crash-replay tooling.

## What it's for

Systematically fuzzing SIP devices for robustness vulnerabilities. VoIPER (Python 2, GPLv2, 2008, by nnp of unprotectedhex.com; SourceForge project voiper.sourceforge.net, source mirrored on GitHub at gremwell/voiper) centres on a fuzzing suite built on the Sulley framework that covers SIP INVITE (three test suites), smart and dumb ACK and CANCEL fuzzers (which first push the target into a state expecting the request), NOTIFY, SUBSCRIBE, REGISTER and SDP — over 200,000 generated tests across the RFC 3261 attributes. Around the fuzzer sit a SIP torturer based on RFC 4475, protocol- and process-based crash detection (a Sulley process monitor watches the target and pauses the fuzzer on a crash), support for clients that demand registration before fuzzing, and a crash-replay tool that turns recorded crashes into standalone proof-of-concept scripts. A Windows GUI exists; on Linux (and in BlackArch's wrappers `voiper-fuzzer`, `voiper-torturer`, `voiper-crash-replay`) it is command-line driven. For single-request mutation fuzzing instead of a generation framework see [[siparmyknife]].

## Typical use cases

### Fuzz a SIP device with crash detection

```
voiper-fuzzer -f SIPInviteCommonFuzzer -c 2 -i 198.51.100.10 -p 5060 -a sessions/scen2 -m 1024
```

Outcome: runs the INVITE common-attribute fuzzer against the target with process-based crash detection level 2 (the fuzzer pauses when the target crashes), fuzz strings capped at 1024 bytes to stay inside a typical 1500-byte UDP MTU, and session state written under `sessions/scen2` — where each crash produces a `.crashlog` file. Run with `-c 0` first for a plain robustness bombardment without target management.

### Pick a fuzzer

```
voiper-fuzzer -l
```

Outcome: lists every available fuzzer by name (per request type and smart/dumb state handling) for use with `-f`; `voiper-fuzzer -l SIPInviteCommonFuzzer` prints what that specific fuzzer does.

### Replay recorded crashes or emit a proof of concept

```
voiper-crash-replay -d sessions/scen2 -i 198.51.100.10 -p 5060
```

Outcome: re-sends every `.crashlog` in the directory to the target to confirm each crash reproduces; replacing `-d` with `-r -f sessions/1_134.crashlog -o poc.py` instead writes a standalone Python PoC script you can run as `python poc.py 198.51.100.10 -p 5060` — the artifact to attach to a bug report.

### Run RFC 4475 torture traffic

```
voiper-torturer -i 198.51.100.10 -p 5060
```

Outcome: sends the RFC 4475 torture-suite messages — malformed and edge-case SIP — to the target as a standards-robustness check complementing the random fuzzing campaigns.

*No corpus evidence — derived from upstream documentation.*
