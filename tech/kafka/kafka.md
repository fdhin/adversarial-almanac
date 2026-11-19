---
technology: "Apache Kafka"
kind: overview
audience: offensive-security
family: messaging
status: "thin (2 evidence files pass the re-gated command-context gate, both fingerprint/adjacent-tier — upstream carries the methodology)"
layer: distributed event-streaming platform (brokers on 9092/tcp; legacy ZooKeeper metadata on 2181/tcp)
language: "Java/Scala (broker); JVM clients + librdkafka clients"
homepage: "https://kafka.apache.org"
github-repo: "apache/kafka"
provenance: "field-grounded THIN (2 evidence files pass the re-gated command-context gate: 9092/tcp, kcat/kafkacat, kafka-console*, bootstrap-server, zookeeper-shell/zkCli, ZooKeeper command-context, 2181/tcp; chains: a 9092/tcp open port table — nmap labels it XmlIpcRegSvc, the IANA registrant, not Kafka; process table shows :::9092 LISTEN — fingerprint-tier; a log-viewer app exposing a download endpoint for ZooKeeper logs at 127.0.0.1:3923/zk_logs — adjacent-tier; chisel tunnels reusing port 9092 and hex/timestamp collisions excluded) + upstream-verified: kcat README (kafkacat rename Aug 2021, -L metadata mode, -P produce, -b/-t/-p/-G/-X flags), IANA port registry (9092/tcp = XmlIpcRegSvc 'Xml-Ipc Server Reg', 2006 — unrelated to Kafka, explains nmap labels), Apache Kafka docs security page (listener protocols PLAINTEXT/SSL/SASL_PLAINTEXT/SASL_SSL; SASL mechanisms GSSAPI/PLAIN/SCRAM-SHA-256/512/OAUTHBEARER; listener.security.protocol.map; inter-broker defaults to PLAINTEXT), Kafka 4.0 removed ZooKeeper (KRaft-only, per Confluent/Conduktor timelines), 2026-11-12."
---

# Apache Kafka

> A message pipeline where every microservice's secrets eventually flow as plaintext events — and whose broker, left in default PLAINTEXT, hands the whole topic list and every message to any client that can reach 9092.

## What it is

Kafka is the de-facto distributed event-streaming platform: producers append **events** to **topics** (partitioned, replicated logs), consumers read them back, and a cluster of **brokers** coordinates (bootstrap on **9092/tcp**). Historically a separate **ZooKeeper** (2181/tcp) held cluster metadata (controller election, topic configs, ACLs in a znode); since KIP-500/KRaft, brokers hold metadata in an internal Raft topic, and **Kafka 4.0 removed ZooKeeper entirely** (upstream-verified). The migration is your dating tool: a 2181 port beside 9092 means an old, pre-4.0 estate.

The operator's mental model: **one port, one binary protocol, optional auth bolted on per-listener**. Brokers can expose different listeners with different security protocols — `PLAINTEXT`, `SSL`, `SASL_PLAINTEXT`, `SASL_SSL` via `listener.security.protocol.map` — and **inter-broker traffic defaults to PLAINTEXT** (upstream-verified). The danger shape is the common one: a PLAINTEXT listener on 0.0.0.0:9092 with no ACLs, because "the network is internal". Clients that can reach it can enumerate topics and read/write events with zero credentials — kcat (the artist formerly known as kafkacat) makes that a one-liner.

Fingerprinting is the whole observed story: one port table (`9092/tcp open XmlIpcRegSvc?` — that's the IANA registrant name, not a Kafka detection; nmap cannot fingerprint Kafka's binary protocol) and one log-viewer leaking ZooKeeper logs through an internal HTTP endpoint. Everything actionable below is upstream-derived from the Kafka docs and kcat.

## The anatomy that matters on an engagement

    scan → 9092/tcp open (nmap: 'XmlIpcRegSvc?' or unknown — binary protocol, no banner)
         → ZooKeeper 2181 alongside?  → pre-4.0 estate, ZK admin surface too
    bootstrap client (kcat / kafka-console-*) → PLAINTEXT listener?
         → metadata (-L): brokers, topics, partitions          ← free recon on any open broker
         → consume (-C/-G): every event in every readable topic  ← the data leak
         → produce (-P): injected events downstream services trust  ← DESTRUCTIVE
    auth present? SASL handshake → credential hunt (JAAS files, client.properties in repos/CI)
    post-shell on a host → server.properties / kafka_server_jaas.conf → listeners, SASL users

Three structural properties drive most findings:

- **PLAINTEXT is the default listener and the default inter-broker protocol.** Upstream: security protocols are per-listener and inter-broker defaults to PLAINTEXT — estates opt in to security per listener, and the internal listener often stays open.
- **Metadata needs no credentials on an open broker.** kcat `-L` lists brokers, topics and partitions — the recon step doubles as the "is auth actually on" probe (anonymous metadata answers = unauthenticated data plane).
- **Topics are a secrets inventory.** Event streams carry what services emit: credentials, tokens, PII, audit logs — reading a topic is rarely a dead end, and consumers downstream often trust input blindly.

## Where you'll meet it

- **Microservice estates** — Kafka as the backbone: one broker hop yields auth events, order streams, and internal service chatter (an adjacent observed pattern: big-data stacks expose ZooKeeper/Hadoop log viewers through internal apps).
- **Big-data pipelines** — ZooKeeper (2181) and Hadoop/HBase services cluster around the brokers on internal dashboards; old estates leak `znode` data (ACLs, configs) through unauthenticated ZK (`zkCli.sh` connects without auth on default installs).
- **Cloud/managed (MSK, Confluent)** — SASL/SSL enforced, so the finding class shifts to credential handling: JAAS files, `client.properties` in repos, CI variables, over-broad ACLs.
- **Post-shell on any JVM host** — `server.properties` reveals `listeners`/`advertised.listeners`/`listener.security.protocol.map` and JAAS files carry SASL users; consumer configs leak bootstrap credentials.

## Reading the server's mind

1. **9092 with no HTTP** — Kafka's protocol is binary and banner-less; nmap shows `XmlIpcRegSvc?` (the IANA registrant of 9092, unrelated to Kafka — verified) or nothing: fingerprint by *client*, not banner (observed: `9092/tcp open XmlIpcRegSvc?`).
2. **`kcat -L` succeeding anonymously** = PLAINTEXT listener, no SASL challenge: the entire enumeration+read chain applies immediately.
3. **2181 next to 9092** = ZooKeeper-era (pre-4.0) cluster: `zkCli.sh`/`echo dump | nc` on 2181 enumerates brokers and consumers when ZK is unauthenticated.
4. **`listeners=PLAINTEXT://:9092` in server.properties** (post-shell read) = the misconfiguration in one line; `SASL_PLAINTEXT` = credentials exist but traffic is readable on-path.
5. **SASL mechanism from a captured handshake or config** sets the attack: `PLAIN` (static users in JAAS), `SCRAM` (dictionary-attackable), `GSSAPI` (Kerberos — ticket machinery in the kerberos unit).

## Where to go next

[[kafka-bug-bounty]] — the phased agent methodology: port truth vs nmap labels, kcat metadata/produce/consume workflows, ZooKeeper adjacency, SASL listener analysis, and the secrets-in-topics data-leak patterns.
