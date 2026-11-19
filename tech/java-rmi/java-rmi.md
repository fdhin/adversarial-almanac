---
technology: "Java RMI"
kind: overview
audience: offensive-security
family: network-services
layer: "Java remoting: JRMP protocol, rmiregistry naming (1099), JMX connectors, Java-serialization wire format"
language: Java
first-released: "JDK 1.1 (1997)"
license: "GPLv2 with Classpath exception (OpenJDK)"
homepage: "https://docs.oracle.com/en/java/javase/21/docs/specs/rmi/index.html"
github-repo: "openjdk/jdk"
evidence: "4 evidence files after command-context gate (the placeholder sample over-included two files whose '1099' hits were file sizes (1099KB) and missed two others the marker count skipped; see ../internal coverage ledger). Thin unit; upstream supplies the protocol model"
status: thin-enriched
provenance: "field-mined from 4 engagements in the evidence pool (see ../internal coverage ledger) + upstream-verified: Oracle rmiregistry tool docs (default port 1099, LocateRegistry/Naming operations), frohoff/ysoserial README (payload list incl. JRMPClient/JRMPListener; Spring bean-factory gadget absent from stock list), qtc-de/beanshooter README (enum/standard/tonka operations), nmap.org NSE rmi-vuln-classloader page (default-config remote class loading, portrule 1098/1099/1090/8901-8903), Metasploit auxiliary/gather/java_rmi_registry module page. Accessed 2026-11-12"
---

# Java RMI

> A remote-object broker whose registry happily tells you every service name it hosts, invokes methods you hand it (serialized objects and all), and — in default configurations — loads classes from URLs it is told to trust. Java deserialization with a directory service bolted on.

## What it is

Java Remote Method Invocation (JDK 1.1, 1997, now OpenJDK) is Java's native remoting: servers export objects over the JRMP protocol; a **registry** (the `rmiregistry` command, port 1099 unless told otherwise) maps names to remote stubs; clients call `LocateRegistry.getRegistry()`, `lookup(name)`, then invoke methods whose arguments and returns are **Java-serialized objects**. JMX (Java Management Extensions) rides on RMI — the `jmxrmi` connector exposes an MBean server over the same machinery, commonly on its own port (1090 and friends).

The operator's mental model: **an unauthenticated object bus with serialization at both ends**. The registry's protocol is not access control — anyone who can reach the port can `list()` the bound names, `lookup()` stubs, and invoke methods with crafted arguments. Whatever the remote object's classpath holds becomes your gadget library; whatever its methods accept becomes your deserialization sink. On JMX endpoints the method you most want already exists: MBeans that load more MBeans from remote URLs, or execute commands outright.

The takeaway: RMI is found inside Java-heavy estates (application servers, JMX monitoring, multi-service products) and almost always unauthenticated when found. The observed chains split evenly between three moves — enumerate and invoke through the registry, own an exposed JMX console, or stand up your own listener and make the target deserialize on connect. Nmap flags `java-rmi` on ports like 1099/1090, but any Java service port may be speaking JRMP.

## The anatomy that matters on an engagement

    client → [registry :1099] --list()/lookup()--> stub --> [exported object :any] --invoke(args…)-->
      args = Java serialization  ← gadget chain territory
    misbehavior lives at:
      1. registry: unauthenticated list/lookup/rebind (recon + service invocation map)
      2. method invocation: serialized args deserialize on a classpath full of libraries
      3. default-config remote class loading (rmi-vuln-classloader class of RCE)
      4. JMX connector (jmxrmi): no-auth MBean servers → deploy/execute MBeans
      5. attacker-side listeners: JRMPListener serving gadgets to connecting clients (two-stage)

Three structural properties drive most findings:

1. **Enumeration is free.** The registry's whole purpose is to name services. `list()` returns every bound name; each stub names its interfaces and home port — a complete internal service map in one packet exchange (observed: a custom client did lookup + rebind in minutes).
2. **Everything on the wire is serialized.** Method arguments deserialize inside the server JVM. Post-JEP-290 JVMs filter some classes, but application classpaths keep the gadget library large (observed: ysoserial gadgets delivered both directions).
3. **JMX is RMI with an API for code deployment.** An MBean server that accepts connections without auth — the common lab/production misconfig — lets you load your own MBean (command executor) through documented JMX operations (observed: beanshooter deployed a TonkaBean shell).

## Where you'll meet it

- **Enterprise Java servers** — application platforms exporting internal services; JMX monitoring left bound to all interfaces (`com.sun.management.jmxremote` properties without `*.ssl`/`*.authenticate` hardening).
- **Product internals on odd ports** — observed: `java-rmi` on 2222 (a custom JMX endpoint), a registry on 9002 behind a web app. Trust the protocol probe, not the port number.
- **Tomcat and servlet containers** — the manager text/JMX interfaces are HTTP-fronted JMX (observed: log-forging through the JMX interface, unauthenticated, CSRF-exempt "intended for tools").
- **Attacker-side infrastructure** — your own JRMP listener is part of chains against OTHER Java surfaces (Spring bean-factory `rmi://` URLs resolving from the target — observed: an XML bean factory fetched `rmi://ATTACKER_IP/test` and deserialized the answer).

## Reading the framework's mind

- `rmiregistry` starts on 1099 by default (`rmiregistry [port]`); `LocateRegistry.getRegistry(host, port)` + `Naming.list()/lookup()/rebind()` are the documented client surface — rebind included, so registry write access lets you hijack other clients' lookups.
- nmap's `rmi-vuln-classloader` tests the classic default: rmiregistry configurations that permit loading classes from remote codebases → straight RCE ("vendor classifies this as a design feature"). Its portrule covers 1098, 1099, 1090, 8901-8903 for services named `java-rmi`/`rmiregistry`.
- ysoserial ships dedicated RMI payloads: `JRMPClient` (makes the target connect out to your listener) and `JRMPListener` (the listener that serves gadgets to whoever connects); helper mains target the registry and JMX directly. The Spring bean-factory gadget seen in the field comes from extended builds — it is not in stock ysoserial's payload list.
- JEP 290 serialization filters (modern JDKs) block many registry-delivered gadgets — but application-level RMI endpoints and JMX MBean operations are frequently still reachable; downgrade to invoking real methods with benign-but-informative arguments before declaring a dead end.
- JMX no-auth is a configuration, not a bug class name: check `com.sun.management.jmxremote.authenticate=false` in the target's JVM flags (`ps` output) — that single flag is the whole finding.

## Where to go next

[[java-rmi-bug-bounty|Java RMI bug-bounty methodology]] — phased: port/protocol fingerprint → registry enumeration → remote object invocation (custom client) → JMX attacks (beanshooter) → deserialization delivery (ysoserial, JRMP listener) → tool mapping.
