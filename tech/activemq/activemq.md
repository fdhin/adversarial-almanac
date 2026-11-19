---
technology: "Apache ActiveMQ"
kind: overview
audience: offensive-security
family: messaging
language: java
first-released: 2004
license: Apache-2.0
homepage: "https://activemq.apache.org"
github-repo: "apache/activemq"
provenance: "upstream-grounded (corpus evidence: 1 writeup — port-marker hits were hex collisions); facts verified against Apache advisories 2026-11-12"
---

# Apache ActiveMQ

> The veteran Java message broker that speaks a deserializing binary protocol and ships an admin console with default credentials — two pre-auth doors into the JVM.

## What it is

Apache ActiveMQ (2004, Apache Software Foundation) is the original open-source JMS message broker — the middleware that decouples Java applications via queues and topics. Operationally it is one JVM process that simultaneously listens on several ports, each a different wire protocol: **OpenWire** (61616, the native Java protocol), AMQP (5672), MQTT (1883), STOMP (61613), and an embedded Jetty web server on **8161** hosting the admin console at `/admin` and the REST/file APIs. Since 2023 "ActiveMQ" also means **ActiveMQ Artemis** (the next-generation broker, console on 8161, different internals) — check which one you are facing, because their histories diverge.

The operator takeaway is that both of its doors have carried pre-auth criticals. The wire protocol deserializes Java classes by design: **CVE-2023-46604** (CVSS 9.8) lets an unauthenticated OpenWire packet make the broker instantiate any class on its classpath with a String argument — and the broker ships Spring's `ClassPathXmlApplicationContext`, whose constructor happily fetches an application-context XML over HTTP from the attacker. The console door: the legacy **fileserver** web app (5.0.0–5.13.x) allowed unauthenticated `PUT` plus `MOVE` to any filesystem path the broker user could write — webshell-into-`webapps/` RCE and crontab persistence were one-liners (CVE-2016-3088). Both are years old and still found in the wild; the console default credentials `admin`/`admin` outlive both.

## The anatomy that matters on an engagement

    client → 8161 (Jetty: /admin console, /api REST, /fileserver ≤5.13.x)
         → auth: JAAS basic realm (default admin/admin; users.properties)
    client → 61616 (OpenWire) → marshaller instantiates classes from the wire
         → 5672 AMQP / 1883 MQTT / 61613 STOMP (per transport config in activemq.xml)
    broker user writes: KahaDB (data/), webapps/ (JSP executes!), conf/jetty.xml + credentials.properties

Three properties drive most findings:

- **The wire protocol is a deserialization surface.** OpenWire marshalling trusting class names from the peer is the whole CVE-2023-46604 story; protocol-level, pre-auth, no console credentials involved.
- **The webapps directory is executable storage.** Anything that can write a file under `webapps/` (fileserver MOVE, console abuse, KahaDB host access) is code execution as the broker user.
- **Everything runs as one user.** Broker, console, Jetty, and your webshell are the same OS identity; credentials files (`conf/credentials.properties`, `jetty-realms.properties`) sit next to the data.

## Where you'll meet it

- **Enterprise Java estates** — order processing, integration buses; boxes that "are just a queue" and were excluded from patch cycles. Highest concentration of ≤5.13 (fileserver) and 5.15–5.18.2 (OpenWire RCE) versions.
- **IoT/MQTT edge deployments** — ActiveMQ as an MQTT broker, sometimes paired with an exposed 1883 (anonymous plugins are a misconfiguration class of their own).
- **Kubernetes-era redeploys** — Artemis instances in clusters; the console is still Jetty with JAAS, and default credentials travel with copied manifests.

## Reading the broker's mind

1. **`admin`/`admin` is the default credential pair** on `/admin` — try it before anything clever; the login succeeds and the footer states the exact 5.x version.
2. **`61616/tcp open apachemq` in nmap output is conclusive** for the OpenWire listener; CVE-2023-46604 applies to 5.18.2 and below (fixed 5.15.16/5.16.7/5.17.6/5.18.3).
3. **The fileserver exists only on 5.0.0–5.13.x** — enabled by default before 5.12, disabled-but-configurable in 5.12–5.13, removed in 5.14.0. Its presence is itself a version oracle.
4. **The console reveals the topology** — queues, topics, subscribers, and connection URLs once inside; that is reconnaissance data about the surrounding estate.
5. **Artemis ≠ Classic** — Artemis carries the OpenWire code but no Spring, so CVE-2023-46604 has no known exploit chain there; identify the broker before reporting.

## Where to go next

[[activemq-bug-bounty|ActiveMQ bug bounty methodology]] — fingerprinting and version oracles, console attacks, the fileserver PUT/MOVE chain, the OpenWire RCE with a safe verify recipe, and evidence handling.
