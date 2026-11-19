---
technology: "Java Debug Wire Protocol (JDWP)"
kind: overview
audience: offensive-security
family: java
layer: "JVM debugger transport (dt_socket): unauthenticated, unencrypted packet protocol; method invocation as a protocol command; convention ports 8000 (Tomcat JPDA), 5005, 8787"
language: Java (protocol spans all JVM vendors and versions)
standards: "JDWP, part of the Java Platform Debugger Architecture (JPDA)"
homepage: "https://docs.oracle.com/en/java/javase/21/docs/specs/jdwp/jdwp-protocol.html"
github-repo: "https://github.com/hugsy/jdwp-shellifier" (exploitation client)
evidence: "zero corpus evidence by design — upstream-only new unit (bucket-2, owner-approved)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-jdwp-java-debug-wire-protocol.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: zero corpus evidence by design (new-unit bucket-2; built from the upstream reference plus primary sources, not the writeup corpus). Upstream-verified 2026-11-13: IOActive 'Hacking the Java Debug Wire Protocol' (Alladoum, 2014 — protocol properties, five-step exploitation chain, IBM JDK banner-first behavior, nmap jdwp-exec IDSizes caveat, tested against Oracle JDK 1.6/1.7, OpenJDK 1.6, IBM JDK 1.6); hugsy/jdwp-shellifier README (current usage: -t/-p/--break-on/-c, JDK 11 output sample); Oracle JDWP/JPDA specifications; nmap nsedoc jdwp-exec (jdwp-exec.cmd argument, JDWPSystemInfo class injection). Rewritten from HackTricks (src/network-services-pentesting/pentesting-jdwp-java-debug-wire-protocol.md), CC BY-NC 4.0, accessed 2026-11-13."
---

# Java Debug Wire Protocol (JDWP)

> A JVM debug transport with no authentication and no encryption: complete a 14-byte handshake and you hold debugger privileges — list loaded classes, set breakpoints, and invoke methods on the live heap. An exposed JDWP port is code execution wearing a debugger's badge.

## What it is

JDWP is the wire protocol of the Java Platform Debugger Architecture — the thing your IDE talks to when you attach to a remote JVM. A debuggee opts in at launch with `-agentlib:jdwp=transport=dt_socket,server=y,...` (legacy spellings `-Xdebug -Xrunjdwp:` on very old JVMs); it then listens on whatever port the operator picked — 8000 is only Tomcat's convention, and the protocol has no affinity for any port number. The protocol itself is a packet-based binary format, mostly synchronous request/reply, with asynchronous event notifications (breakpoints) flowing back.

Everything an attacker needs is in the design, stated plainly in the IOActive research that turned this into a standard exploitation target: **no authentication, no encryption**. Debugger commands can enumerate all loaded classes and their methods, read and write stack values, set breakpoints, and — the money primitive — *invoke methods*, both static (`ClassType/InvokeMethod`) and on live objects (`ObjectReference/InvokeMethod`). Nothing is memory-corrupted; the protocol simply grants the powers a debugger legitimately has, to whoever connects. Command execution lands with the privileges of the JVM process.

The operator's mental model: **open JDWP = reliable RCE with attacker-side tooling only**. The classic chain (IOActive's five steps, still how every client does it): fetch a reference to `java.lang.Runtime` and its `getRuntime()` method from the class list; arm a breakpoint on a method guaranteed to fire (default: `java.net.ServerSocket.accept`); trigger it; in that live-thread context, allocate your payload string and invoke `Runtime.getRuntime().exec(...)`. Every step is a documented protocol command.

## The anatomy that matters on an engagement

    TCP connect → send "JDWP-Handshake" (14 ASCII bytes) → peer echoes it back
        ▼
    VirtualMachine/IDSizes + Version   ← exact JVM fingerprint (vmVersion, vmName)
        ▼
    VirtualMachine/AllClasses + ReferenceType/Methods   ← find java.lang.Runtime.getRuntime()
        ▼
    EventRequest/Breakpoint on java.net.ServerSocket.accept (or String.indexOf)
        ▼  trigger the service (a single connection usually suffices)
    Event/Composite hands you a live thread reference
        ▼
    CreateString(payload) → ClassType/InvokeMethod(getRuntime) → ObjectReference/InvokeMethod(exec)
        ▼
    OS command as the JVM's OS user

Structural properties that drive findings:

1. **Method invocation is a first-class command** — the exploit never needs a bug in the target code, only a reachable debug transport.
2. **A breakpoint supplies the live-thread context** the invocation requires; the choice of breakpoint method (traffic frequency) is the main reliability lever.
3. **The handshake is the entire "authentication"** — a one-probe banner grab that both finds and confirms the service.

## Where you'll meet it

- **Dev/staging JVMs promoted to production** — a developer's `JAVA_OPTS`/`JAVA_TOOL_OPTIONS` debug flag baked into a container image or systemd unit; Tomcat started with `catalina.sh jpda start` (port 8000).
- **Containerized Java apps** where the debug port was published "just for troubleshooting".
- **Fleet scans**: the handshake reply is distinctive, and IBM JDK builds have been observed *speaking first*, so passive banner collection (Shodan's `JDWP-Handshake` queries) finds them; masscan-style sweeps of 8000/8080/8787/5005 surface the rest.
- thinnest coverage: nobody files "debug port open" as a finding until you show the one-command shell.

## Reading the framework's mind

1. The handshake is vendor- and version-invariant: any JVM echo of `JDWP-Handshake` is a positive identification, and the `VirtualMachine/Version` reply hands you the exact `vmVersion`/`vmName` (e.g. `OpenJDK 64-Bit Server VM, 11.0.16`) — fingerprint before choosing payload tooling.
2. Tooling must honor `IDSizes` per target — nmap's `jdwp-exec` NSE historically hardcodes structure sizes and fails on many JVMs (IOActive's caveat); prefer a client that negotiates.
3. The invocation primitive is not version-gated: demonstrated from JDK 1.6 through current builds (the maintained shellifier shows JDK 11 output). Old JVMs additionally require the `-Xdebug` flag before any debug transport works.
4. Reliability ladder: breakpoint on `java.lang.String.indexOf` beats `ServerSocket.accept` (fires on almost any traffic); an uploaded binary executed once beats repeated debugger invocations.
5. Loopback-bound or SSH-tunnelled debug transports are *less exposed*, not authenticated — the protocol gate is reachability, nothing else. See the [[jdwp-bug-bounty|methodology]] for the jdb flow when you want stock-JDK tooling only.

## Where to go next

- [[jdwp-bug-bounty|JDWP bug bounty methodology]] — detection probes, the shellifier and jdb workflows, stability tradeoffs.
- Adjacent JVM surfaces: [[java-rmi]], [[java-deserialization]], [[log4j]].
