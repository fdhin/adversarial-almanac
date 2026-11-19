---
technology: GlusterFS
kind: overview
audience: offensive-security
status: upstream-only
family: storage
layer: distributed filesystem
language: C
homepage: https://www.gluster.org
github-repo: https://github.com/gluster/glusterfs
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — ports and access model verified against docs.gluster.org Setting-Up-Clients (24007–24008 TCP+UDP, brick ports 49152+ replacing 24009+, Gluster 10 base-port..max-port randomization, native/NFSv3/CIFS access, volfile fetch behavior); CVE-2018-1088 verified against Red Hat article 3414511 (unauth shared-storage mount, snap_scheduler.py symlink, detection commands, RHSA-2018:1136/1137); CVE-2022-48340 against the Red Hat CVE record; CVE-2023-26253 against gluster/glusterfs issue 3954 + rewritten from HackTricks (src/network-services-pentesting/24007-24008-24009-49152-pentesting-glusterfs.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# GlusterFS

> A distributed filesystem whose architecture trusts the network: by design any client that can reach the pool may mount any volume — and one special volume has historically held a file that root's cron parses.

## What it is

GlusterFS aggregates storage from multiple servers into a single namespace. Member servers form a **trusted storage pool** of peers, each contributing **bricks** (exported directories) that volumes stitch together; clients reach the data through the FUSE-based native client, NFSv3, or CIFS. There is no per-user authentication in the native path — the boundary is the mount itself, and by architecture any client that can reach the pool can mount any volume unless an administrator narrows that with per-volume CIDR filters (`auth.allow`/`auth.reject`) or TLS with certificate identity checks.

The port map matters for scoping: the **glusterd** management daemon answers on **24007–24008** (TCP and UDP), and brick data connections use one port per brick — historically **24009** upward in old deployments, later releases moved the base to **49152** to follow IANA conventions, and from Gluster 10 brick ports are randomized within the `base-port` to `max-port` range configured in `glusterd.vol`. A node can expose 24007 while hosting no brick for your target volume at all — management-plane reachability and data-plane reachability are separate findings.

One mount behavior from the upstream docs pays for itself on engagements: the server named in a `mount -t glusterfs` command is only used to fetch the volume's configuration file (the **volfile**); afterwards the client talks directly to every server that volfile lists, which may not include the one you mounted through. Your client traffic fans out across the whole pool.

The operator takeaway: exposed glusterd means remote peer and volume enumeration without credentials, and an unrestricted volume means read-write access to a filesystem other machines trust — including, on 3.x-era servers with snapshot scheduling ever enabled, a root-owned cron file living inside the special `gluster_shared_storage` volume (CVE-2018-1088).

## The anatomy that matters on an engagement

    attacker → 24007/tcp (glusterd management)
      peer status / volume info — pool membership + volume names, bricks, options
    attacker → 49152+ (brick ports, or 24009+ on old builds; randomized in 10+)
      mount -t glusterfs HOST:VOL — auth boundary is the mount, not a login
      volfile from HOST lists every peer: traffic fans out across the pool
    special volume: gluster_shared_storage
      consumed by geo-replication and snapshot scheduling
      3.x snapshot scheduler: /etc/cron.d symlink → file inside this volume

Three structural properties drive findings: (1) the management plane enumerates the estate pre-authentication in default deployments; (2) authorization is address- or certificate-based, not identity-based — any host inside `auth.allow` (or anywhere, if unset) gets full read-write; (3) cluster features place root-consumed files *inside* mountable volumes, so a data-plane mount can become server-side execution (the snapshot-scheduler chain below).

## Where you'll meet it

- **Scale-out file backends** for virtualization, media processing, and render farms — big flat namespaces, permissive defaults, and operators who rarely touch `auth.allow`.
- **Kubernetes and private-cloud storage nodes** repurposed from general servers — brick ports drift from the documented map as pools grow.
- **Geo-replication / DR pairs** — `gluster_shared_storage` follows these deployments, carrying the historical cron-symlink risk with it.
- **Legacy 3.x pools kept alive for capacity reasons** — exactly where the CVE-2018-1088 preconditions accumulate.

## Reading the framework's mind

1. `gluster --remote-host <host> peer status` and `volume info all` interrogate a remote glusterd without pool membership (see [[glusterfs-bug-bounty]] Phase 0–1).
2. `volume info` shows the options that will decide your mount attempt: `auth.allow` CIDRs and TLS/`transport.socket.ssl` settings — read them before trying.
3. A volume named `gluster_shared_storage` is a feature volume, not user data; mounting it read-only and inventorying its consumers is the CVE-2018-1088 assessment path.
4. Gluster 10+ randomizes brick ports within `base-port`..`max-port` — never gate a scan on 49152 alone; treat the documented management ports as the anchor.
5. Check `gluster --version` on every node and reconcile against distribution backports before claiming a CVE applies — upstream fix versions and distro package versions diverge.

## Where to go next

The companion methodology: [[glusterfs-bug-bounty]] — remote enumeration, the unauthenticated mount, shared-storage and CVE-2018-1088 assessment, and version triage.
