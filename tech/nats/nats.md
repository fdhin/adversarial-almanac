---
technology: NATS
kind: overview
audience: offensive-security
status: upstream-only
family: messaging
layer: message middleware
language: Go
homepage: https://nats.io
github-repo: https://github.com/nats-io/nats-server
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — ports/protocol facts verified against docs.nats.io (server ports, monitoring endpoints incl. /connz subs/auth params and /jsz, JetStream direct-get API, TLS configuration) + rewritten from HackTricks (src/network-services-pentesting/4222-pentesting-nats.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# NATS

> The message bus that volunteers a JSON résumé the instant you open a socket — and whose clients answer with `CONNECT {"user":..., "pass":...}` in cleartext on a port that was never meant to face an attacker.

## What it is

NATS is a lightweight publish/subscribe message bus written in Go (created at Apcura/Apcera by Derek Collison, CNCF-hosted today). Its wire protocol is deliberately trivial text: on TCP connect the server speaks first with an `INFO { ... }` JSON banner, the client replies with a `CONNECT {...}` frame carrying its credentials, then the session is just `PUB`/`SUB` lines addressed to dot-separated subjects. **JetStream** bolts a persistence layer (streams, consumers) onto the same listener, which is what makes the broker worth looting rather than merely sniffing. TLS and authentication are both optional, and internal deployments routinely run plaintext username/password auth.

Three sockets matter: **4222/tcp** for clients, the conventional cluster-route port **6222/tcp**, and an optional HTTP **monitoring port** on **8222/tcp** — the monitoring port is off until an operator sets `http_port` in the config or passes `-m` on the command line, and it serves plain unauthenticated JSON.

The operator takeaway: NATS was designed for trusted internal networks, so nearly every interesting disclosure — version, auth posture, JetStream state, live subscriptions, and in plaintext deployments the credentials themselves — is available to anyone who can reach the right socket or intercept the right name resolution.

## The anatomy that matters on an engagement

    client → 4222/tcp
      1. server speaks first:  INFO {"server_id":..., "version":..., "auth_required":...,
                               "jetstream":..., "max_payload":..., "tls_required":...}
      2. client replies:       CONNECT {"user":"...", "pass":"..."}     ← cleartext unless TLS
      3. PUB/SUB on subjects:  dot tokens; `*` matches ONE token, `>` the trailing tree
      4. Core NATS never replays — a subscriber only sees messages published AFTER it
         subscribed; retained history lives only in JetStream streams
    client/ops → 8222/tcp (http_port / -m 8222)
      /varz (process), /connz (clients; ?subs=true&auth=true), /routez (cluster), /jsz (JetStream)
    filesystem → JetStream store directory
      stream state + message payloads on disk — reachable via any file-read/NFS/mount primitive

The structural properties that drive findings: (1) the banner handshake leaks posture before any authentication; (2) the monitoring HTTP port exposes live subject names and authenticated users by default; (3) clients trust DNS to find the broker and then send credentials immediately — a spoofable name is a credential harvest.

## Where you'll meet it

- **Microservice meshes and event backbones** replacing heavier brokers — the plaintext-CONNECT mistake lives here; one captured frame is a foothold into every service that publishes secrets on its subjects.
- **Edge/IoT and Kubernetes deployments**, including leaf nodes bridging clusters — the monitoring port tends to follow the server everywhere it is copied.
- **Internal tooling dashboards** scraping 8222 — from a pivot host, the monitoring endpoints are a free map of accounts, subjects, and JetStream stream names before you hold any credential.

## Reading the framework's mind

1. The `INFO` banner arrives before you send anything — `version`, `auth_required`, `jetstream`, `max_payload`, `tls_required` are a complete pre-auth fingerprint (see [[nats-bug-bounty]] Phase 0).
2. `auth_required:false` on an open 4222 means a wildcard subscribe (`>`) is free intelligence on every message flowing through that account.
3. Core NATS has no history — retained messages exist only as JetStream streams, so `stream view`/`stream get` on discovered stream names is the looting path, not re-subscription.
4. The monitoring endpoints are unauthenticated and node-local: `/connz?subs=true&auth=true` names connected users and their subscriptions; `/jsz?accounts=true&streams=true` names streams; in a multi-node cluster each server only reports what it sees.
5. Plaintext deployments plus DNS-resolved broker names make credential capture by impersonation trivial — and correctly validated TLS on the client side is precisely what defeats it.

## Where to go next

The companion methodology: [[nats-bug-bounty]] — banner triage, monitoring-port harvesting, DNS impersonation, JetStream looting, and direct message-get for narrow accounts.
