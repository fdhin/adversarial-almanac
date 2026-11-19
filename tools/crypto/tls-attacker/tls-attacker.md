---
name: "tls-attacker"
categories:
  - blackarch-crypto
version: "9246.931058ef3"
homepage: "https://github.com/tls-attacker/TLS-Attacker"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: java
license: Apache
meta-checked: "2026-11-09"
github-repo: "tls-attacker/TLS-Attacker"
github-stars: 880
github-pushed: "2026-11-09"
github-archived: false
github-issues: 40
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 405
gh-activity-checked: "2026-11-09"
---

# tls-attacker

> Java framework for crafting arbitrary TLS message flows — hand-tuned ClientHellos, malformed fields, complete custom handshakes — and executing them against any TLS server or library.

## What it's for

TLS-Attacker lets you specify exactly which TLS messages are sent, in what order, and with which field values, including deliberately malformed ones, then run that flow against a TLS peer and inspect the responses. It supports SSL 3 through TLS 1.3 (partial SSL 2), around 300 cipher suites and 30 extensions, and can act as either client or server. It is aimed at TLS library developers and pentesters checking how an implementation tolerates protocol violations; there is no GUI and no green/red verdicts — the demo applications are thin wrappers over a programming library, and full power comes from defining workflows in Java or XML. Distro packages ship the demo JARs behind wrapper binaries (`tls-attacker-Attacks`, `tls-attacker-TLS-Client`, `tls-attacker-TLS-Server`, `tls-attacker-TLS-Mitm`, `tls-attacker-TLS-Forensics`, `tls-attacker-TraceTool`) that pass arguments straight through to the corresponding jar.

## Typical use cases

### Drive a handshake with a chosen cipher suite and protocol version

Run a controlled handshake offering exactly the parameters you want to test.

```
java -jar apps/TLS-Client.jar -connect target.example.com:4433 -cipher TLS_RSA_WITH_AES_256_CBC_SHA -version TLS11
```

Outcome: executes a client handshake against target.example.com:4433 restricted to the given cipher suite and TLS version, logging each message sent and received; a handshake that fails or aborts at a specific message shows the server refusing those parameters (note TLS-Attacker does not fully honor server-selected cipher suites, so specify one explicitly).

### Bring up TLS-Attacker's own TLS server

Start a listener whose protocol stack you control for testing TLS clients.

```
java -jar apps/TLS-Server.jar -port 4433
```

Outcome: opens a TLS listener on port 4433 implemented by the TLS-Attacker stack, giving you a server peer whose message flow can be customized with the same workflow and config options as the client.

### Replay a custom message flow defined in XML

Execute an arbitrary WorkflowTrace instead of a standard handshake.

```
java -jar apps/TLS-Client.jar -connect target.example.com:4433 -workflow_input workflow.xml
```

Outcome: runs the sequence of Send/Receive actions described in workflow.xml — for example a ClientHello followed by a heartbeat request whose payload length is overwritten to 20000 to test Heartbleed-style handling; the Receive actions state what the server is expected to answer, so the first violated expectation pinpoints where the implementation deviates.

*No corpus evidence — derived from upstream documentation.*
