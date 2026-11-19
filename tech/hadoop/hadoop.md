---
technology: "Apache Hadoop (HDFS & YARN)"
kind: overview
audience: offensive-security
status: upstream-only
family: big-data
layer: distributed storage & compute
homepage: https://hadoop.apache.org
github-repo: https://github.com/apache/hadoop
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design — owner-approved bucket-2 new-unit wave 2026-11-13; ports per hadoop.apache.org ClusterSetup, YARN REST/WebHDFS semantics per hadoop.apache.org docs, CVE-2023-26031 gate verified against hadoop.apache.org/cve_list.html (fixed 3.3.5), NSE script names per nmap.org/nsedoc + rewritten from HackTricks (src/network-services-pentesting/50030-50060-50070-50075-50090-pentesting-hadoop.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Hadoop

> A cluster whose compute layer is arbitrary-command-execution-as-a-service by design — YARN's job is to run your submitted code somewhere in the rack, and in the default `simple` security mode the only identity it checks is one you type into the request.

## What it is

Apache Hadoop is the open-source distributed storage-and-processing stack behind enterprise data lakes: **HDFS** (a NameNode keeping metadata, DataNodes keeping blocks, replicated across the cluster) for storage, **YARN** (ResourceManager scheduling containers, NodeManagers executing them) for compute, MapReduce as the classic processing model on top. Clusters are herds of Linux hosts holding the organization's aggregated analytics data — the data plane that marketing, fraud, and data-science teams feed with everything sensitive they collect.

The security posture that matters is a single configuration fork. **`simple`** mode (the default for many internal and "secured network" deployments) trusts the asserted operating-system or `user.name` identity on the request — there is no handshake, no ticket, nothing. **Secure mode** wraps HDFS, YARN, and MapReduce in Kerberos. In `simple` mode the surfaces below are effectively unauthenticated; whether that is a finding depends on authorization config (HDFS permissions, YARN queue ACLs, proxy-user rules), not on an auth wall that is not there.

The operator takeaway: an exposed YARN ResourceManager is the crown-jewel primitive. Submitting an application whose ApplicationMaster command is `/bin/sh -c ...` is not an exploit — it is the platform's documented API (that is what the DistributedShell example application does). Unauthenticated clusters have been mass-harvested this way for cryptomining for a decade.

## The anatomy that matters on an engagement

    HDFS:  NameNode (metadata) ←→ DataNodes (blocks)
      50070 NameNode web UI / WebHDFS  (3.x default: 9870)
      50075 DataNode web UI            (3.x: 9864)
      50090 Secondary NameNode
    legacy MapReduce v1 (still scanned):
      50030 JobTracker · 50060 TaskTracker
    YARN:
      8088  ResourceManager web UI + REST   ← POST an application = run a command
      8042  NodeManager web UI
      8031/8032  YARN RPC (scheduler/app-client) — often forgotten, same submission surface on older clusters
    auth model:
      simple → ?user.name=hdfs is the whole identity assertion (WebHDFS) / unauth pseudo-user on RM REST
      kerberos → Negotiate tickets (curl --negotiate -u :)

Structural properties that drive findings: (1) one REST POST is a command shell on some NodeManager — the RCE is first-party functionality, so no patch fixes it; (2) every web UI leaks cluster intelligence (versions, config, HDFS tree) before any auth question; (3) the forgotten RPC ports (8031/8032) repeat the REST submission surface for tools that speak protobuf.

## Where you'll meet it

- **Corporate data-lake subnets** — big Linux host fleets, HDFS/2.x-3.x ports open across the rack, co-located with [[kafka]] and [[elasticsearch]] doing the plumbing.
- **Research, university, and legacy analytics clusters** — built pre-Kerberos, "secured" by network position that has since eroded.
- **Cloud-adjacent big-data estates** — self-managed Hadoop beside the managed services, often the forgotten half.

## Reading the framework's mind

1. WebHDFS takes `user.name` as a query parameter in `simple` mode — the identity model is *the request says who you are*, subject to HDFS file permissions and proxy-user settings, not to authentication.
2. The YARN submission contract is two POSTs: `new-application` for an ID, then the app with an `am-container-spec` command — any queue ACL that accepts you is code execution somewhere in the cluster.
3. The pre-2010 web ports (50030/50060/50070/50075/50090) are so characteristic that nmap ships an NSE script for each — a full panel of them on one host is a signature no other product produces.
4. The NameNode's filesystem tree is browsable through LISTSTATUS — the data lake's layout (and the config files at `/etc/hadoop/`) is reconnaissance gold.
5. Hadoop 3.3.1–3.3.4 on Linux carries CVE-2023-26031: the SUID `container-executor` resolves libraries through a relative RUNPATH, so a user who can run containers (including a remote submitter on an insecure cluster) can plant `libcrypto.so` and take root on the NodeManager.

## Where to go next

The companion methodology: [[hadoop-bug-bounty]] — NSE enumeration, unauth HDFS browsing and exfil through WebHDFS, the YARN application-submission RCE on 8088 and 8031/8032, the container-executor local root, and Kerberos-mode fallbacks. Related: [[kafka]] and [[elasticsearch]] (co-located data-plane services), [[kerberos]] (secure mode).
