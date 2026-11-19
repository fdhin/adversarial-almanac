---
technology: "Java insecure deserialization (native serialization + gadget-chain ecosystem)"
kind: overview
audience: offensive-security
family: java
layer: JVM object serialization — an in-process library surface reachable through many carriers (HTTP, RMI, JNDI, cookies, DB wire protocols, ViewState)
language: Java
homepage: "https://github.com/frohoff/ysoserial"
github-repo: "frohoff/ysoserial"
evidence: "9 writeups (command-context gate: ysoserial invocations, CommonsCollections/CommonsBeanutils gadget names, rO0AB/ACED0005 stream markers, marshalsec, javax.faces.ViewState, ObjectInputStream attack code, javaSerializedObject; loose 'deserialization' word matched 47 machines — gate detail in internal coverage ledger). Six seed candidates excluded at gate: .NET chains (ysoserial.net), out of scope."
provenance: "corpus-mined from 9 writeups in the writeup corpus + upstream-verified: frohoff/ysoserial README payload table and CommonsCollections5.java source, Apache Commons Collections security page (COLLECTIONS-580), CVE-2015-7501 (Red Hat Bugzilla 1279330), OFBiz advisories CVE-2020-9496 / CVE-2023-49070 / CVE-2023-51467 (lists.apache.org). Owner-approved class framing like code-runner; the jackson-databind unit proposal gate-failed and surfaced this cluster — see internal-coverage/pass2-report.md. Pass 2 final unit, 2026-11-13."
---

# Java deserialization (native streams + gadget chains)

> Anywhere a JVM calls `ObjectInputStream.readObject()` on bytes you control, the four magic bytes `AC ED 00 05` are a remote code-loading primitive — and a decade of public gadget chains means the classes that turn that primitive into command execution are usually already on the classpath.

## What it is

Java's native serialization turns an object graph into a self-describing byte stream (`AC ED 00 05` magic, then class descriptors and field data), and `ObjectInputStream.readObject()` rebuilds the graph — invoking `readObject`/`readResolve` hooks and, critically, loading every class the stream names. Deserializing attacker bytes was never safe: the stream format is simultaneously a *class-loading* and *method-scheduling* instruction set. The 2015 "Marshalling Pickles" work and the follow-up that showed WebLogic, WebSphere, JBoss, Jenkins and friends all shipped the same gadget classes (CVE-2015-7501 era) turned this from a theoretical language smell into the default explanation for any Java box that falls over instantly.

The attack has two halves that live in different places. The **sink** — an `ObjectInputStream` fed network bytes — is the actual vulnerability: hidden in a servlet that reads a request body, a JSF ViewState decoder, an XML-RPC endpoint that accepts a `<serializable>` extension element, an RMI/JMX endpoint, a message queue, a session-persistence loader, a fat client's private protocol, or a database client deserializing a column. The **gadget chain** is reusable ammunition: a prearranged object graph (CommonsCollections, CommonsBeanutils, JRE-internal classes, or the application's own `readObject` hooks) that walks from `readObject()` to `Runtime.exec()` or `TemplatesImpl` bytecode definition. The canonical generator is ysoserial (`java -jar ysoserial.jar CommonsCollections5 'command'`); the ysoserial authors state the principle precisely: the vulnerability lies in the application performing unsafe deserialization, NOT in having gadgets on the classpath.

For an operator this means: find the sink first (it is the finding you report), then inventory the classpath to pick the chain (it is merely how you prove impact). Both halves fingerprint cleanly — the stream magic `AC ED 00 05` raw or `rO0AB` in base64 is unmistakable on the wire, and gadget compatibility is a deterministic function of library and JDK versions.

## The anatomy that matters on an engagement

    your bytes  (AC ED 00 05 ...  |  base64 rO0AB...)
      -> carrier: HTTP body/param, cookie, JSF ViewState, XML-RPC <serializable>,
                  RMI/JRMP, JNDI javaSerializedObject (rogue LDAP), session
                  persistence file, DB result-set column, message queue
      -> ObjectInputStream.readObject() on attacker data   <-- the vulnerability
      -> stream class descriptors load classes already on the classpath
      -> gadget chain walks library/JRE/app objects to a sink method
      -> end gadget: Runtime.exec(String)  or  TemplatesImpl._bytecodes -> defineClass
      -> command runs as the JVM user (often the whole box on legacy deployments)

Three structural properties drive most findings:

- **The sink is where nobody audits.** Serializers get wired into "remember me" cookies, inter-server RPC, batch file drop directories and cache/DB columns years before anyone considers them request-surface. Anything that accepts opaque blobs is a candidate; the magic bytes confirm.
- **Gadget reachability is a classpath question, not a skill question.** `commons-collections` 3.x on the classpath plus a modern-JDK-friendly entry class (CC5/CC6/CC7) is still the dominant configuration; `commons-collections4` changes which chains apply (CC2/CC4); the JRE-only `Jdk7u21` chain needs an old JDK exactly. Version intel (lib directories, `pom.xml`, shaded JARs) converts directly into chain selection.
- **Error behavior leaks the sink.** A tampered stream that answers `ClassNotFoundException: SomeClass`, `InvalidClassException` (serialVersionUID mismatch) or a raw stack trace naming `ObjectInputStream` has just confirmed deserialization of your bytes — before any gadget is needed.

## Where you'll meet it

- **Java fat clients** (Swing/JavaFX over HTTPS): the client serializes objects into request bodies; decompiling the client shows the `ObjectInputStream` server-side contract, and patching the client to send a ysoserial payload is the standard path.
- **JSF/MyFaces applications**: the `javax.faces.ViewState` hidden field is a serialized (and optionally encrypted+MACed) component tree by default; with server-side state saving and leaked `org.apache.myfaces.SECRET`/`MAC_SECRET` (config backups, readable `web.xml`), you forge ViewStates wholesale.
- **Apache OFBiz ERP** deployments: the `/webtools/control/xmlrpc` endpoint accepts a base64 serialized object inside an XML-RPC `<serializable>` extension element — a pre-auth deserialization CVE family (see methodology for the verified advisory chain).
- **Spring/enterprise XML endpoints** accepting Castor/Hessian unmarshalling: the marshalsec toolkit generates XML-wrapped gadgets (e.g. `SpringAbstractBeanFactoryPointcutAdvisor` steering a JNDI URL) for a dozen unmarshallers.
- **RMI registries, JMX consoles, legacy JBoss** (`JMXInvokerServlet`): serialized objects are the native protocol; a ysoserial JRMP listener makes the target connect to you.
- **Databases and caches**: JDBC drivers and custom clients deserialize values fetched from a server you can impersonate (rogue MySQL answering a legit connection request), and serialized blobs parked in DB columns/tables get deserialized by background jobs.
- **JNDI lookups you trigger**: post-2019 JDKs block remote codebases from LDAP/RMI URLs, so JNDI exploitation shifted to serving a `javaSerializedObject` — a ysoserial payload — instead of a class reference; any JNDI injection (log4j being the mass case) ends in this unit's gadgets.

## Reading the framework's mind

1. **The magic never lies**: `AC ED 00 05` at stream start, `rO0AB` at the start of its base64 form, `aced0005` in hex dumps — grep proxy traffic and parameter values for these before doing anything clever.
2. **`Content-Type: application/x-java-serialized-object`** names the sink outright; more often the stream rides a normal POST body, a cookie, or an XML element with no hint — hunt the magic, not the header.
3. **CommonsCollections numbering is a compatibility table**, not a quality ranking: CC1/CC3 want commons-collections 3.x and pre-8u71-style JDK `AnnotationInvocationHandler` internals; CC5 needs JDK 8u76+ and no SecurityManager; CC6/CC7 are the broad-JDK entries; CC2/CC4 are the commons-collections4 lines ending in `TemplatesImpl` bytecode execution.
4. **JSF ViewState protection is only as good as its key custody**: MyFaces encryption keys are `web.xml` context parameters (`org.apache.myfaces.SECRET`, `MAC_SECRET`, `MAC_ALGORITHM`) — config-read primitives convert directly into ViewState forgery.
5. **Generate payloads with the target's JDK family** — stream and `serialVersionUID` compatibility differ across releases, which is why replicating the target stack in a container to build the payload is a real technique, not paranoia.
6. **Scope note**: the .NET sibling (ysoserial.net: `TypeConfuseDelegate`, `ObjectStateFormatter`/ViewState with `machineKey`, `BinaryFormatter` chains) is a different runtime, tool and fingerprint set — deliberately out of this unit's scope. On this vault's evidence, a jackson-databind-anchored unit was proposed and failed its gate (the signal collapsed); the surviving cluster is the native-serialization surface documented here (see `internal-coverage/pass2-report.md`).

## Where to go next

[[java-deserialization-bug-bounty|Java deserialization bug bounty methodology]] — sink-hunting workflow, gadget selection from classpath intel, carrier-specific delivery recipes, and canary-first verification. Neighbor units: [[java-rmi]] (RMI/JRMP/JMX endpoints as carriers and listeners) and [[log4j]] (JNDI injection whose modern exploitation ends in exactly these serialized gadgets).
